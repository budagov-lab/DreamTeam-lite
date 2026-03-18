---
name: start
description: Start dreamteam-lite: save full goal text after /start, plan in Left, then ping-pong Left↔Right until all tasks are done.
---

You are the **dreamteam-lite Start command**.

## Input
The user invoked this command as: `/start` followed by the full goal text on the same line.
Your job is to capture everything after `/start` as GOAL_TEXT (trim only leading whitespace).

## Output requirements
- Always keep console/UI messages in English.
- Do not ask the user questions.

## Steps
1. Create/overwrite `goal.json`:
   - `id`: string (use ISO-8601 timestamp)
   - `title`: first ~80 characters derived from the same goal text
   - `description`: store GOAL_TEXT verbatim
   - `created_at`: ISO-8601 timestamp
2. Create/overwrite `state.json`:
   - `active_orchestrator = "left"`
   - `planning_complete = false`
   - `run_status = "running"`
   - `context_token_threshold = 8000` (default)
   - `context.left.approx_tokens = 0`
   - `context.right.approx_tokens = 0`
3. Dispatch **Left** for planning:
   - `mcp_task` with `subagent_type: "orchestrator-left"`
   - prompt: "Plan dreamteam-lite tasks from goal.json. Return BATCH_DONE or ALL_COMPLETE."
4. Interpret the result:
   - If `ALL_COMPLETE`: tell user "All tasks are complete." and stop.
   - If `BATCH_DONE`: set `active_orchestrator = "right"` in `state.json`.
5. Ping-pong loop until completion:
   - Dispatch the active orchestrator each time.
   - Orchestrator returns `BATCH_DONE` or `ALL_COMPLETE`.
   - On `BATCH_DONE`: flip `active_orchestrator` (`left` ↔ `right`) and continue.
   - On `ALL_COMPLETE`: tell user "All tasks are complete." and stop.

