## DreamTeam Lite (Cursor plugin)

DreamTeam Lite is a Cursor plugin for developers and vibe-coders who want to turn an idea into finished tasks without losing context.
It keeps your goal and task state in sync, automatically routes work between specialized agents,
and helps you continue from where you left off after interruptions.

### What this plugin does

- Converts one high-level goal into a trackable delivery flow (`Planner -> Developer -> Reviewer`).
- Uses state-driven orchestration so work continues predictably instead of stalling in chat context.
- Automatically alternates orchestration sides (`Left <-> Right`) while preserving progress.
- Recovers from interrupted sessions by resuming `in_progress` work first.
- Handles failed task attempts with bounded retries and explicit unfinished reasons.
- Stores all runtime state in plain JSON files inside `.dreamteam-lite/`:
  - `.dreamteam-lite/goal.json` — immutable project goal.
  - `.dreamteam-lite/tasks.json` — list of tasks created by the Planner and updated by the orchestrators.
  - `.dreamteam-lite/state.json` — active orchestrator, batch routing markers, and run status.
- Keeps state human-readable and auditable, so you can inspect and control the workflow at any time.

There is **no external engine** here: all orchestration lives inside Cursor agents and JSON.

Although this is a compact demo project, it has enough practical autonomy to solve small real tasks end-to-end, not just showcase an orchestration pattern.  
It was tested with the models typically available in Cursor after hitting normal limits (yes, the free-tier fallback ones) — so it does not depend on top-tier models to be useful.
[![Watch the demo](https://img.youtube.com/vi/M8LF81M9rS8&t=2222s/0.jpg)](https://www.youtube.com/watch?v=M8LF81M9rS8&t=2222s)

### Relationship to the main DreamTeam project

- The original DreamTeam engine and architecture live at  
  `https://github.com/budagov-lab/DreamTeam`.
- DreamTeam Lite:
  - **reuses the high-level pattern** (Dispatcher + Left/Right, planning vs execution),
  - but **removes** the SQLite task DAG, advanced maintenance agents, analytics dashboard, and CLI.
  - is designed as a practical learning project and a starting point for experiments.

If you need the full autonomous development stack with large task graphs, dashboards, and advanced runtime tooling, use the main DreamTeam project.  
If you want a lightweight, Cursor-native version of the orchestration pattern, DreamTeam Lite is built for that.

### Usage (conceptual)

- Primary entrypoints after plugin install:
  - `/start-dreamteam <FULL GOAL TEXT>` to initialize goal/state and trigger planning.
  - `/run-dreamteam` to continue execution batches until completion.
- Project-local behavior:
  - Runtime files are stored under `.dreamteam-lite/` in the current workspace only.
  - At startup, the Left Orchestrator creates `.dreamteam-lite/` if it does not exist.
  - Task state is never shared between different repositories/projects.
- Dispatcher core contract remains `/start` and `/run` internally:
  - Goal capture for start is still **all text immediately after `/start`** (verbatim).
  - Skills apply the same contract without spawning a separate `dispatcher` subagent.
- Current execution details:
  - Exactly one task is processed per orchestrator call.
  - If Reviewer returns `CRITICAL`, the same task is retried up to 2 additional times (3 total attempts).
  - If still not approved, task stays `needs_changes` with `unfinished = true` and `unfinished_reason`.
  - On resume, tasks with `status = "in_progress"` are prioritized first.
  - If `.dreamteam-lite/state.json` or `.dreamteam-lite/goal.json` is malformed (e.g., concatenated JSON objects), agents normalize it to one valid object and continue.

All runtime instructions and UI/console messages must be in **English**. The documentation here is
also in English so that the plugin can be shared easily.

