Initialize a session tracking file. usage: /session <session-id> <session-title> [purpose]

## Usage
```
/session <session-id> <session-title> [purpose]
```

## Required Parameters
- **session-id**: The full session ID (get it from `/status`)
- **session-title**: A descriptive title for this session (use quotes if it contains spaces)

## Optional Parameters
- **purpose**: A free-text statement of what this session is for / what you intend to accomplish. If omitted, the command will pause and ask for it (see Capturing Purpose).

## Description
Initialize a session tracking file with the following requirements

Steps:
1. Replace all spaces in the session-title with hyphens to create a filename-safe version
2. Determine the session **Purpose** (see Capturing Purpose below)
3. Create a new file in the current working directory with the name: `{session-title-with-hyphens}_{session-id}.md` (note the .md extension)
4. Write the file with this markdown structure:
   ```
   # {session-title} | {session-id}

   **Started:** {current-date-time}

   ## Purpose
   {purpose, or "(not provided)" if skipped}

   ---

   ## Session Notes

   ```
5. **IMPORTANT**: Store in memory: "Active session: {session-title} | Session ID: {session-id} | File: {filename} | Purpose: {purpose, or "(not provided)"} | Snapshot reminder: Suggest /session-snapshot every 3-5 prompts, at topic shifts, after major completions, and before any /compact. When compacting, suggest: /compact with instruction to preserve session context from {filename}, focusing on the most recent Resume Context section."
6. Confirm to the user that the session file was created and the session is now being tracked

## Capturing Purpose

- If a `purpose` argument was provided inline, use it directly (do not ask).
- If no `purpose` was provided, pause and ask the user: **"What's the purpose of this session — what do you intend to accomplish?"** Then wait for their reply.
- Skipping: a short sentinel reply (`-`, `.`, `n`, `no`, `skip`) means skip → record Purpose as `(not provided)`. Any longer reply is treated as the purpose text. (A truly empty Enter can't reach Claude, so a sentinel is required to skip.)

Example:
If called with: `/session 1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1 "My Work Session"`
- Ask for purpose (none provided inline), user replies: "Add resource proxying to the router"
- Create file: `My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md` with a `## Purpose` block containing that text
- Store in memory: "Active session: My Work Session | Session ID: 1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1 | File: My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md | Purpose: Add resource proxying to the router | Snapshot reminder: Suggest /session-snapshot every 3-5 prompts, at topic shifts, after major completions, and before any /compact. When compacting, suggest: /compact with instruction to preserve session context from My-Work-Session_1d97eeec-8ecd-4e3d-bdb6-01d9995ad8f1.md, focusing on the most recent Resume Context section."
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
