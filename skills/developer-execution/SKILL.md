---
name: developer-execution
description: Implements a single dreamteam-lite task (code + tests). Does NOT modify tasks.json status.
---

# Developer Execution (Lite)

## When to Use
- The orchestrator dispatches `developer` for one task object.

## Workflow
1. Implement the task deliverable in source files.
2. Run tests (no watch mode) if command execution is available in this Cursor setup.
   - Prefer `pytest` for Python projects.
3. Fix test failures and re-run until passing (or until blocked).
4. Return one line:
   - `DONE. <1 sentence summary>`

## Rules
- Never modify `goal.json`.
- Never modify `tasks.json` status fields.
- No parallelism.
- Console logs and UI messages must be in English.

