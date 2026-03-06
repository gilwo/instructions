Initialize a session tracking file. usage: /session <session-id> <session-title>

## Usage
```
/session <session-id> <session-title>
```

## Required Parameters
- **session-id**: The full session ID (get it from `/status`)
- **session-title**: A descriptive title for this session (use quotes if it contains spaces)

## Description
Initialize a session tracking file with the following requirements

Steps:
1. Replace all spaces in the session-title with hyphens to create a filename-safe version
2. Create a new file in the current working directory with the name: `{session-title-with-hyphens}_{session-id}.md` (note the .md extension)
3. Write the file with this markdown structure:
   ```
   # {session-title} | {session-id}

   **Started:** {current-date-time}

   ---

   ## Session Notes

   ```
4. **IMPORTANT**: Store in memory: "Active session: {session-title} | Session ID: {session-id} | File: {filename} | Snapshot reminder: Suggest /session-snapshot every 3-5 prompts, at topic shifts, after major completions, and before any /compact. When compacting, suggest: /compact with instruction to preserve session context from {filename}, focusing on the most recent Resume Context section."
5. Confirm to the user that the session file was created and the session is now being tracked

Example:
If called with: `/session 1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1 "My Work Session"`
- Create file: `My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md`
- Store in memory: "Active session: My Work Session | Session ID: 1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1 | File: My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md | Snapshot reminder: Suggest /session-snapshot every 3-5 prompts, at topic shifts, after major completions, and before any /compact. When compacting, suggest: /compact with instruction to preserve session context from My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md, focusing on the most recent Resume Context section."
- Confirm: "Session 'My Work Session' initialized and stored in memory. Use /session-snapshot to capture progress. I'll remind you to snapshot periodically."

## Proactive Snapshot Reminders

**Instructions for Claude:** After storing the session in memory, proactively suggest a `/session-snapshot` when any of these conditions are met during the conversation:
- Roughly every 3-5 user prompts since the last snapshot
- A major milestone is completed (feature done, bug fixed, refactor finished)
- The conversation topic shifts significantly
- Before the user runs `/compact`
- Before a risky or destructive operation

When suggesting, say: "It's been a few prompts since the last snapshot. Want me to run /session-snapshot before continuing?"

Do not be pushy — suggest once, then move on if the user declines or ignores.
