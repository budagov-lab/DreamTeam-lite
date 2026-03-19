## DreamTeam Lite (Cursor plugin)

This repository contains **DreamTeam Lite**, a minimal Cursor plugin that demonstrates the
dual-orchestrator dispatcher pattern from the original
DreamTeam project (`https://github.com/budagov-lab/DreamTeam`) in a simplified, self-contained way.

Source repository (this project):
`https://github.com/budagov-lab/DreamTeam-lite`

Canonical reference (pattern and original project):
`https://github.com/budagov-lab/DreamTeam`

> This is **not** a full port of DreamTeam.  
> It is a **small example** that shows how the pattern works (Dispatcher → Left/Right Orchestrators → Planner/Developer/Reviewer)
> using only Cursor agents, rules, skills, and a couple of JSON files.

### What this plugin does

- Installs a **lightweight Dispatcher** plus **Left and Right Orchestrators** into Cursor.
- Uses a simple **Planner → Developer → Reviewer** loop with a manually inspectable JSON task list.
- Stores all state in plain JSON files inside `.dreamteam-lite/`:
  - `.dreamteam-lite/goal.json` — immutable project goal.
  - `.dreamteam-lite/tasks.json` — list of tasks created by the Planner and updated by the orchestrators.
  - `.dreamteam-lite/state.json` — active orchestrator, batch routing markers, and run status.
- Demonstrates the **ping-pong execution loop**:
  - Main chat (via skills) acts as dispatcher and calls **Left** once to plan.
  - After planning, dispatching is state-driven: each orchestrator writes batch markers, then dispatcher calls the opposite side.
  - Each switch conceptually resets the orchestrator’s context while keeping tasks in JSON.

There is **no external engine** here: all orchestration lives inside Cursor agents and JSON.

Although this is a compact demo project, it has enough practical autonomy to solve small real tasks end-to-end, not just showcase an orchestration pattern.  
It was tested with the models typically available in Cursor after hitting normal limits (yes, the free-tier fallback ones) — and it still gets the job done.

### Relationship to the main DreamTeam project

- The original DreamTeam engine and architecture live at  
  `https://github.com/budagov-lab/DreamTeam`.
- DreamTeam Lite:
  - **reuses the high-level pattern** (Dispatcher + Left/Right, planning vs execution),
  - but **removes** the SQLite task DAG, advanced maintenance agents, analytics dashboard, and CLI.
  - is intended only as a **didactic example** and a starting point for your own experiments.

If you need the full autonomous development cruiser with hundreds of tasks, dashboards, and the
Python engine, use the main DreamTeam project. If you only want to understand and play with the
pattern inside Cursor, DreamTeam Lite is designed for that.

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

