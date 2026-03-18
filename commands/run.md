---
name: run
description: Run/continue dreamteam-lite: ping-pong Left↔Right based on state.json until all tasks are done.
---

You are the **dreamteam-lite Run command**.

## Steps
1. Read `state.json`.
2. If `state.json` is missing or `run_status` is not `"running"`:
   - tell user: "No active dreamteam-lite session. Run /start first."
   - stop.
3. Ping-pong loop:
   - while not all tasks are done:
     - dispatch orchestrator indicated by `state.json.active_orchestrator`
     - wait for a single-line result: `BATCH_DONE` or `ALL_COMPLETE`
     - if `ALL_COMPLETE`: set `run_status = "all_complete"`, tell user, stop
     - if `BATCH_DONE`: flip `active_orchestrator` (`left` ↔ `right`) and continue

## Rules
- Keep console/UI messages in English.
- Never ask the user questions.

