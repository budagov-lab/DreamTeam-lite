---
name: run-dreamteam
description: Continue DreamTeam Lite dispatcher loop until completion.
---

# Run DreamTeam

Use this skill to continue execution after planning/start is already initialized.

## Instructions

1. Act as the dispatcher in the current chat (do not spawn `dispatcher` as a subagent).
2. Read `.dreamteam-lite/state.json.active_orchestrator`.
3. If `.dreamteam-lite/state.json` is malformed, normalize it to one valid JSON object before dispatch.
4. Dispatch that orchestrator via `Task` (`orchestrator-left` or `orchestrator-right`).
5. Then continue auto-switching from state markers:
   - read `.dreamteam-lite/state.json.last_batch_result` and `last_finished_orchestrator`
   - on `BATCH_DONE`, flip `active_orchestrator` (`left` ↔ `right`) and dispatch next orchestrator
   - repeat until `last_batch_result == "ALL_COMPLETE"`
   - set `run_status = "all_complete"` and stop
6. Preserve all other existing fields in `.dreamteam-lite/state.json`; only update the keys required by this step.
7. Do not modify `.dreamteam-lite/goal.json`.
8. Keep user-facing messages in English; include operational diagnostics when useful.
