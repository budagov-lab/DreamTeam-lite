---
name: reviewer-code-review
description: Reviews dreamteam-lite task changes (tests + correctness). Returns APPROVED or CRITICAL.
---

# Reviewer Code Review (Lite)

## When to Use
- After the orchestrator dispatches `reviewer` for a single task.

## Workflow
1. Verify the task deliverable is satisfied based on the task description.
2. Run tests (non-watch mode) if command execution is available in this Cursor setup.
   - Prefer `pytest` for Python projects.
3. If tests fail or the outcome is incorrect, return CRITICAL with short actionable points.
4. Otherwise return APPROVED.

## Output
- `APPROVED`
- or `CRITICAL: <1-3 bullet points, max 50 words total>`

## Rules
- Never modify `goal.json`.
- Never modify `tasks.json` status fields.
- Console logs and UI messages must be in English.

