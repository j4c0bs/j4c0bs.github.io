---
title: "The Session Start Hook"
date: 2026-04-26
draft: false
tags: ["claude-code", "agents", "cli"]
summary: "Using Claude Code's SessionStart hook to surface project state and kill stale assumptions between sessions."
---

> **For agents:** This post has a companion gist with copy-paste building blocks and setup instructions — [SessionStart Hook Guide](https://gist.github.com/j4c0bs/621a89f86c12c151a005df9afec4c162).

Claude Code runs shell hooks on specific events. The `SessionStart` hook fires once at the beginning of every conversation. Its output is injected into the system context before you type anything — meaning the agent starts the session already knowing things you'd otherwise have to tell it.

This matters because the single biggest source of friction in multi-session AI work is stale assumptions. The agent doesn't know what day it is, what changed since last time, or whether the project is in a broken state. A session start hook fixes that by surfacing state that silently invalidates assumptions between sessions.

The most useful section here might be [scheduled notes](#scheduled-notes): passive, date-keyed markdown files that surface context when you start a session, not on a timer. They fill a gap that cron and memory systems don't cover.

## What to surface

The sections below are ordered by criticality — things that gate all other work come first.

### Current date and time

```bash
echo "$(date '+%A %Y-%m-%d %H:%M:%S %Z') ($(date -u '+%Y-%m-%dT%H:%M:%SZ'))"
```

Output: `Sunday 2026-04-26 07:12:20 PDT (2026-04-26T14:12:20Z)`

Both local and UTC. The agent has no clock. Without this, it will guess the date from its training data or the last message timestamp — and be wrong. If your project has any time-sensitive logic (scheduled jobs, market hours, deployment windows, cron), this is load-bearing.

### Git status

```bash
GIT_STATUS=$(git status -s 2>/dev/null)
if [ -n "$GIT_STATUS" ]; then
  echo "Uncommitted changes:"
  echo "$GIT_STATUS" | head -10 | sed 's/^/  /'
fi
```

Prevents the agent from stepping on uncommitted work from a prior session. It also catches the "forgot to commit during handoff" case — if the hook shows staged changes, the agent knows to address that before starting new work.

### Recent commits

```bash
echo "Recent commits (last 8):"
git log -8 --pretty=format:'  %h %s' | cut -c1-100
```

Establishes continuity. The agent sees what happened recently and can orient itself without reading session notes. The short hash + subject line is enough — if it needs more detail, it can `git show`.

### Changelog

If your project maintains a changelog, surfacing the latest entry anchors the agent to the current version and the last notable change.

```bash
if [ -f CHANGELOG.md ]; then
  HEADER=$(grep -m1 "^## \[" CHANGELOG.md)
  DESC=$(awk '/^## \[/{f++; next} f==1 && NF{print; exit}' CHANGELOG.md | cut -c1-140)
  echo "Latest changelog: $HEADER"
  [ -n "$DESC" ] && echo "  $DESC"
fi
```

Output: `Latest changelog: ## [1.10.0] — 2026-04-25` followed by a one-line summary.

This works best when the changelog follows a consistent format. We use [Keep a Changelog](https://keepachangelog.com/) with [semver](https://semver.org/). Every entry includes a rationale and before/after metrics. The hook only grabs the headline — the full entry is there if the agent needs to read it.

The changelog lives at `CHANGELOG.md` in the repo root. Entries are added as part of the versioning workflow, not retroactively. The agent knows the format and can write entries that match.

### Scheduled notes

Claude Code has a native `schedule` feature that runs agents at specific times via cron. This is different — scheduled notes are passive, date-keyed markdown files that surface context on the *first session that matches the date*. They don't execute anything.

The distinction matters. A cron schedule fires whether or not you have a session. Scheduled notes fire when you show up. They're for things like "check if the deploy from Thursday caused issues" or "re-evaluate this decision after a week of data" — tasks that need a human-in-the-loop session, not an autonomous run.

Implementation:

```
notes/scheduled/
├── 2026-04-26.md    # surfaces today or any day after
├── 2026-05-01.md    # surfaces starting May 1
├── 2026-10-01.md    # long-dated reminder
└── README.md
```

The hook iterates over files and shows any with a date <= today:

```bash
TODAY=$(date '+%Y-%m-%d')
SCHED_DIR="notes/scheduled"
if [ -d "$SCHED_DIR" ]; then
  for f in "$SCHED_DIR"/2*.md; do
    [ -f "$f" ] || continue
    FILE_DATE=$(basename "$f" .md)
    if [[ ! "$FILE_DATE" > "$TODAY" ]]; then
      echo "[scheduled] $(basename "$f"):"
      sed 's/^/  /' "$f"
    fi
  done
fi
```

The default retention strategy is delete-after-addressing: once all items in a file are complete, the file is removed and deferred items get moved to a later date file. This keeps the directory clean and the hook output focused.

If you prefer to retain addressed notes (for audit trail, retrospectives, etc.), the bash logic needs a small change — match only today's exact date instead of `<=`:

```bash
if [[ "$FILE_DATE" == "$TODAY" ]]; then
```

With retention, older addressed notes should be moved to an archive directory (e.g. `notes/scheduled/archive/`) to avoid accumulating stale output in the hook. Either way, decide the retention strategy upfront and document it where the agent will see it.

## Putting it together

The full hook is a single bash script registered in `.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/session-start.sh"
          }
        ]
      }
    ]
  }
}
```

The script itself follows one rule: output only what changes assumptions. The agent's context window is finite. Every line of hook output competes with the actual conversation. Keep each section to 1-5 lines of output under normal conditions, with verbose output reserved for error states.

A good session start hook is invisible when everything is fine and loud when something is wrong.

## What else could go here

The sections above are general-purpose. Depending on your project, you might add:

- **CI status** — `gh run list --limit 1` to surface a failing build before the agent starts writing code against a broken branch
- **Environment health** — ping a service, check a lockfile, verify a daemon is running
- **Data freshness** — if your project depends on external data, check when it was last updated
- **Blocker detection** — check for lock files, circuit breaker flags, or other hard stops that should halt work before it starts

The pattern is the same: if the agent would otherwise make a wrong assumption, surface the truth in the hook.
