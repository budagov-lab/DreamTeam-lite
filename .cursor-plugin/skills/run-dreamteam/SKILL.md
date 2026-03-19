---
name: run-dreamteam
description: Continue DreamTeam Lite dispatcher loop until completion.
---

# Run DreamTeam

Use this skill to continue execution after planning/start is already initialized.

## Instructions

1. Dispatch the `dispatcher` agent.
2. In the dispatcher prompt, include:
   - "Handle this as `/run`."
   - "Read `state.json.active_orchestrator`."
   - "Loop batch dispatch until `ALL_COMPLETE`."
3. Do not modify `goal.json`.
4. Keep user-facing messages short and in English.
