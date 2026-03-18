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
- Stores all state in plain JSON files inside the workspace:
  - `goal.json` — immutable project goal (written once on `/start`, then read-only for subagents).
  - `tasks.json` — list of tasks created by the Planner and updated by the orchestrators.
  - `state.json` — which orchestrator is active, simple approximate context counters, run status.
- Demonstrates the **ping-pong execution loop**:
  - Dispatcher captures the goal and calls **Left** once to plan.
  - After planning, Dispatcher alternates **Right ↔ Left** while they execute tasks.
  - Each switch conceptually resets the orchestrator’s context while keeping tasks in JSON.

There is **no external Python engine** here: all orchestration lives inside Cursor agents and JSON.

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

- In Cursor: `/start <FULL GOAL TEXT>`
  - The Dispatcher stores **all text immediately after `/start`** into `goal.json.description` (verbatim).
  - Then it calls Left for planning.
- Optional manual overrides:
  - `/addtask <TASK TITLE AND/OR DESCRIPTION>` to append a new pending task into `tasks.json`.
  - `/tasklist` to print current tasks.
- Then: `/run` (and repeat as needed).
  - Dispatcher alternates Left and Right until all tasks in `tasks.json` are done.

All runtime instructions and UI/console messages must be in **English**. The documentation here is
also in English so that the plugin can be shared easily.

