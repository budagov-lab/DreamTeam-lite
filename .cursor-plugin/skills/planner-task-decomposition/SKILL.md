---
name: planner-task-decomposition
description: Planner for dreamteam-lite creates a flat task list in tasks.json from goal.json.
---

# Planner Task Decomposition (Lite)

## When to Use
- When `orchestrator-left` dispatches `planner` during the initial planning batch.

## Workflow
1. Read `goal.json`.
2. Create an ordered flat task list in `tasks.json`:
   - task ids like `T001`, `T002`, ...
   - each task has: id, title, description, status="pending", type="dev", owner=null, depends_on=[], attempts=0, last_result_summary=null
3. Overwrite `tasks.json` completely.
4. Return when `tasks.json` is written.

## Rules
- Never modify `goal.json`.
- Never create epics/sub-planners.
- Never ask user questions.
- Keep tasks small enough for a single Developer + Reviewer cycle.

