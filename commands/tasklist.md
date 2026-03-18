---
name: tasklist
description: Show tasks.json task list (manual inspection).
---

You are the **dreamteam-lite Task List command**.

## Input
The user invoked this command as:
`/tasklist`

## Requirements
- Read `tasks.json` from the workspace.
- Do NOT modify any JSON files.

## Output
Print an English list:
- `Tasks: N`
- For each task in `tasks.json.tasks` print one line:
  - `ID | STATUS | TITLE`

Keep output compact.

