---
title: "Making the Context Window Visible"
date: 2026-05-30
draft: false
tags: ["claude-code", "agents", "cli"]
summary: "A plugin that exposes context window usage to Claude Code agents so they can self-pace instead of guessing."
---

<div class="j4-callout">
<div class="j4-callout-label">For agents</div>
<p>Install from <a href="https://github.com/j4c0bs/claude-ops-plugins/tree/main/plugins/context-tracker">claude-ops-plugins</a>. Run <code>claude-context-pct</code> to check context window usage (0-100%). See the <a href="https://github.com/j4c0bs/claude-ops-plugins/tree/main/plugins/context-tracker">README</a> for statusline setup.</p>
</div>

<div class="j4-tldr">
<div class="j4-tldr-label">TL;DR</div>
<p><code>claude-context-pct</code> returns context window usage as an integer 0-100. Agents check it between tasks to decide whether to keep working or hand off.</p>
</div>

Claude Code agents have no built-in way to check how much of their context window is consumed. Depending on the workflow, this can lead to conservative behavior: asking permission to continue, wrapping up early, or writing premature handoff summaries. This isn't universal. Strictly defined workflows or heavy use of compaction may make context visibility unnecessary. But for open-ended sessions where the agent self-paces across multiple tasks, having a signal helps.

[context-tracker](https://github.com/j4c0bs/claude-ops-plugins/tree/main/plugins/context-tracker) is a Claude Code plugin that exposes a single command, `claude-context-pct`, returning an integer 0-100. The agent calls it, sees "23%", and keeps working. Or sees "74%" and starts wrapping up (depending on your instructions).

## How it works

The plugin has two parts: a **producer** that writes context data to a temp file on each statusline render, and a **consumer** script that reads it.

The consumer is the entire `bin/claude-context-pct`:

```bash
#!/bin/bash
_sid="${1:-$CLAUDE_CODE_SESSION_ID}"
_file="/tmp/claude-$(id -u)/context/${_sid}.json"
_pct=$(jq -r '.used_pct' "$_file" 2>/dev/null) && echo "${_pct}%" || echo "-1"
```

The producer is a block you add to your [statusline script](https://docs.anthropic.com/en/docs/claude-code/status-bar). Claude Code passes a JSON blob to statusline scripts on every render that includes `context_window.used_percentage`. The producer extracts that value and writes it to `/tmp/claude-<uid>/context/<session_id>.json`:

```bash
# --- BEGIN context-tracker (claude-ops-plugins) ---
_ctx_dir="/tmp/claude-$(id -u)/context"
{ read -r _ctx_session_id; read -r _ctx_pct; read -r _ctx_cwd; } <<< "$(echo "$input" | jq -r '
  (.session_id // ""),
  (.context_window.used_percentage // 0 | floor),
  .workspace.current_dir
')"
if [ -n "$_ctx_pct" ] && [ -n "$_ctx_session_id" ]; then
    mkdir -p "$_ctx_dir" 2>/dev/null
    chmod 700 "$_ctx_dir" 2>/dev/null
    _ctx_tmp=$(mktemp "$_ctx_dir/.ctx.XXXXXX")
    printf '{"session_id":"%s","used_pct":%s,"cwd":"%s","updated_at":"%s"}\n' \
        "$_ctx_session_id" "${_ctx_pct:-0}" "$_ctx_cwd" "$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
        > "$_ctx_tmp"
    mv -f "$_ctx_tmp" "$_ctx_dir/${_ctx_session_id}.json"
fi
# --- END context-tracker (claude-ops-plugins) ---
```

The atomic write (write to temp, then `mv`) avoids partial reads if the agent checks while the statusline is updating.

## Limitations

<div class="j4-warning">
<div class="j4-warning-label">Scope</div>
<p>This only reports the <strong>main agent's</strong> context window. Claude Code doesn't expose context usage to subagents, background agents, or team members. A subagent calling <code>claude-context-pct</code> will get the parent's value, not its own. The skill is designed for the top-level agent only.</p>
</div>

## What changes

Instead of asking the user "should I keep going?" or preemptively writing a handoff summary, the agent checks its usage after finishing a task and decides for itself. The user stops being a context-window oracle.

Two things worth noting from early use:

**Don't check often.** Checking mid-task adds noise to the conversation. The useful pattern is checking after completing a unit of work, not during. The skill description intentionally doesn't prescribe check frequency. Project-level instructions (CLAUDE.md) are the right place to define thresholds and actions.

**Usage isn't linear.** Early tasks consume more context because project files are loaded for the first time. Once cached, subsequent tasks are cheaper. A task that costs 10% early in a session might cost 3% later. Early readings will overestimate future consumption. The value can also decrease after auto-compaction, so it's not monotonically increasing.

## Setup

Install the plugin and configure thresholds in your project's `CLAUDE.md`. The producer snippet above is bash, but the statusline script path and language are configurable. If yours is Python, TypeScript, or something else, Claude can adapt the logic. The only requirement is writing the JSON file to the expected path. The plugin gives agents visibility but deliberately does not define what to do with it. Some patterns that work:

- **Periodic check-ins**: "Run `claude-context-pct` after completing each major task to decide whether to continue or hand off."
- **Handoff threshold**: "When context usage reaches N%, wrap up the current task, commit progress, and prepare a handoff summary."
- **Subagent delegation**: "If context exceeds N%, prefer delegating work to subagents. Their intermediate work stays in their own context window; only the result summary returns to the parent."

The plugin is part of [claude-ops-plugins](https://github.com/j4c0bs/claude-ops-plugins) and works on macOS and Linux. Full setup instructions are in the [README](https://github.com/j4c0bs/claude-ops-plugins/tree/main/plugins/context-tracker).
