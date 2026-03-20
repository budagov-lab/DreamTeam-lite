## DreamTeam Lite (Cursor plugin)

DreamTeam Lite is a Cursor plugin for developers and vibe-coders that helps execution move autonomously.
It keeps your goal and task state in sync, routes work between specialized agents,
and helps you continue from where you left off after interruptions.

### Workflow (Dispatcher is the central switch)
The main chat acts as the Dispatcher: it initializes `state.json`, then uses `last_batch_result` and `last_finished_orchestrator` written by orchestrators to decide which side (Left/Right) runs next.
Each orchestrator completes a unit of work and writes fresh batch markers back to `state.json`, so the loop keeps going without manual babysitting.

### Add Plugin Manually (Local)
If you want to add DreamTeam Lite to Cursor without publishing/installing from the marketplace, copy the plugin bundle into Cursor’s local plugins folder.

1. In this repository, locate `dreamteam/.cursor-plugin/`.
2. Create the folder:
   - `%USERPROFILE%\.cursor\plugins\local\dreamteam-lite\`
3. Copy the whole `dreamteam/.cursor-plugin/` folder into:
   - `%USERPROFILE%\.cursor\plugins\local\dreamteam-lite\`
4. Verify that `plugin.json` exists at:
   - `%USERPROFILE%\.cursor\plugins\local\dreamteam-lite\.cursor-plugin\plugin.json`
5. Restart Cursor (or run Developer: Reload Window).
6. In any workspace, open Cursor chat and test:
   - `/start-dreamteam <FULL GOAL TEXT>`
   - then `/run-dreamteam` (if execution was not completed in the first step).

After start/run, runtime state files are created in the current workspace under `.dreamteam-lite/`.

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

Even though this is a compact demo, it is built to be practically useful end-to-end: small tasks can be completed without babysitting, and the workflow keeps its shape under typical Cursor model limits.

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

### Autonomy Proof (Snake from zero)

- Goal: `create a simple Snake game for playing in browser`
- Prompt: one user prompt to start the whole workflow
- Execution: `AUTO` under Cursor free-tier limits
- Result: a complete playable Snake game shipped from scratch (no manual babysitting)
- Runtime: ~37 minutes
- Evidence: generated `.dreamteam-lite/state.json` ended with `run_status = "all_complete"` and `.dreamteam-lite/tasks.json` ended with `status = "done"` for all tasks

Video (full run):
[![Watch how this Cursor plugin builds a Snake game autonomously](https://img.youtube.com/vi/M8LF81M9rS8&t/0.jpg)](https://www.youtube.com/watch?v=M8LF81M9rS8&t=2222s)

