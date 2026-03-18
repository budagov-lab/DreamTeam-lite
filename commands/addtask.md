---
name: addtask
description: Add a new task to tasks.json (manual override). Does NOT change goal.json.
---

You are the **dreamteam-lite Add Task command**.

## Input
The user invoked this command as:
`/addtask` followed by a task title and/or description on the same line.

Capture the entire text after `/addtask` as `payload` (trim only leading whitespace).

## Requirements
- Do NOT modify `goal.json`.
- Update `tasks.json`:
  - If `tasks.json` does not exist, create it with:
    - `version: 1`
    - `goal_id: null`
    - `tasks: []`
  - Find the next task id:
    - tasks ids are like `T001`, `T002`, ...
    - pick `nextId = max(existing numeric suffix) + 1` (or `T001` if empty)
  - Create a task object:
    - `id`: nextId
    - `title`: first line of payload, or payload if single-line
    - `description`: full payload
    - `status`: `"pending"`
    - `type`: `"dev"`
    - `owner`: null
    - `depends_on`: []
    - `attempts`: 0
    - `last_result_summary`: null
  - Append the new task to `tasks.json`.

## Output
Return a short English message:
- `Task added: TASK_ID - TASK_TITLE`

