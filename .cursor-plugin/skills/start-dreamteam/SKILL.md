---
name: start-dreamteam
description: Start DreamTeam Lite dispatcher run. Use when user wants to begin workflow from a natural-language goal.
---

# Start DreamTeam

Use this skill when the user wants to start DreamTeam Lite with a goal.

## Instructions

1. Treat all user text after `/start-dreamteam` as the goal text.
2. Act as the dispatcher in the current chat (do not spawn `dispatcher` as a subagent).
3. Create or overwrite `.dreamteam-lite/goal.json` and `.dreamteam-lite/state.json` using this exact state shape:
   - `active_orchestrator = "left"`
   - `planning_complete = false`
   - `run_status = "running"`
   - `last_finished_orchestrator = null`
   - `last_batch_result = null`
   - (no token counters are required)
4. Immediately dispatch `orchestrator-left` via `Task` (`subagent_type: "orchestrator-left"`).
5. After that, auto-switch orchestrators using `.dreamteam-lite/state.json` markers:
   - read `last_batch_result` and `last_finished_orchestrator`
   - when `BATCH_DONE`, switch `active_orchestrator` to opposite side and dispatch next orchestrator
   - continue until `last_batch_result` becomes `ALL_COMPLETE`
   - then set `run_status = "all_complete"`
6. Do not ask extra questions unless goal text is empty.
7. Keep user-facing messages short and in English; report only final outcome.
