Capture a progress snapshot for the active session and append it to the session file.

## Usage

```
/session-snapshot
```

## Parameters

None - uses the active session stored in memory.

## Prerequisites

- An active session must be initialized with `/session` command first
- Session ID and filename should be stored in memory

Steps:

1. Retrieve the active session information from memory (Session ID and filename)
2. If no active session found in memory, ask the user for the session ID or tell them to run `/session` first
3. Locate the session file: `*_{session-id}.md` in the current working directory
4. If file not found, inform the user and ask them to verify the session was initialized
5. Review the full conversation history carefully before generating the snapshot
6. Count existing snapshots in the file to determine the next snapshot number
7. Generate a progress snapshot following the format below
8. Append the snapshot to the session file
9. After appending, output a suggested `/compact` command (see Post-Snapshot Compact Suggestion)
10. Confirm to the user that the snapshot was appended successfully

## Snapshot Format

```
## Progress Snapshot {N} - {timestamp}

### Executive Summary
[7-10 sentences summarizing overall session progress, goals achieved, and current state]

### Decisions & Rationale
- **[Decision]:** [What was chosen] -- *Why:* [reasoning, tradeoffs considered]
[repeat for each significant decision; omit section if no notable decisions were made]

### Dead Ends & Pivots
- **[Approach tried]:** [Why it failed or was abandoned] -- *Pivoted to:* [what replaced it]
[omit section entirely if none occurred]

### Prompt History

#### Prompt N: [brief title]
**Goal:** [what the user asked for]
**Actions:** [what was done -- files read, created, modified, commands run]
**Outcome:** [result -- what worked, what didn't, what was learned]
**Status:** Completed | Interrupted | In Progress

[repeat for each prompt since the last snapshot]

### Files Changed
- `path/to/file` -- [created|modified|deleted]: [one-line description of change]
[repeat for each file; omit section if no files were changed]

### Open Questions & Blockers
- [Any unresolved issues, pending decisions, or items needing follow-up]
[omit section if none]

### Resume Context
[A dense 5-8 sentence paragraph written FOR Claude, containing everything needed
to continue this session after compaction or in a new conversation. Include: current
goal, what has been done so far, what is in progress, key files involved, architectural
context, and the immediate next step. This must be self-contained -- assume the reader
has zero prior context about this session.]

---
```

## Post-Snapshot Compact Suggestion

After each snapshot, output the following to the user:

```
Snapshot captured. If you'd like to compact now, use:
/compact Preserve session context from {filename}. Focus on the most recent Resume Context: {paste the Resume Context paragraph you just wrote}
```

This gives the user a ready-to-use compact command with the right instructions baked in.

## Post-Compaction Re-orientation

If Claude detects that a compaction has just occurred (conversation history is short, or context feels reset) and there is an active session in memory:

1. Read the session file from disk
2. Find the most recent Resume Context section
3. Announce: "Resumed from snapshot {N}. {one-line summary}. Ready to continue."

## Keyword-Based Auto-Triggering

**Instructions for Claude:** When the user mentions any of these phrases during conversation:

- "track session progress"
- "capture progress"
- "session snapshot"
- "save session progress"
- "record progress"

**Action to take:**

1. Detect the keyword/phrase
2. Ask the user: "Would you like me to capture a session snapshot now?"
3. If user confirms (yes/ok/sure/etc.), automatically invoke `/session-snapshot`
4. If unsure whether the user wants a snapshot, ask for clarification

This enables semi-automatic progress tracking based on natural conversation flow.

## Notes

- This command works with the active session stored in memory
- Use this command whenever you want to capture current progress
- Multiple snapshots can be appended to the same file over time
- The Resume Context section is critical -- write it as if briefing a new developer joining mid-project
- The Decisions & Rationale section captures *why*, not just *what* -- this is the most valuable part for future reference