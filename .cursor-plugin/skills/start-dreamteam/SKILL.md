---
name: start-dreamteam
description: Start DreamTeam Lite dispatcher run. Use when user wants to begin workflow from a natural-language goal.
---

# Start DreamTeam

Use this skill when the user wants to start DreamTeam Lite with a goal.

## Instructions

1. Treat all user text after `/start-dreamteam` as the goal text.
2. Dispatch the `dispatcher` agent.
3. In the dispatcher prompt, include:
   - "Handle this as `/start`."
   - "Goal text is everything after the command token."
   - "Write `goal.json.description` from that captured text."
4. Do not ask extra questions unless goal text is empty.
5. Keep user-facing messages short and in English.
