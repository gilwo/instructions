Export the current conversation to a detailed session file without requiring a pre-initialized session. Use this when you need to preserve context before compacting or ending a conversation.

## Usage
```
/export-session [session-id]
```

## Parameters
- **session-id** (optional): A session ID to include in the filename. If omitted, checks memory for an active session ID first.

## Prerequisites
None -- this command works without `/session` initialization.

Steps:
1. Determine the current working directory
2. Check memory for an active session ID. If found, use it. If not, use the optional `session-id` parameter. Either or neither may be available.
3. Create an `_sessions` directory in the current working directory if it doesn't exist
4. Generate a filename:
   - With session ID: `_sessions/session-export-{YYYY-MM-DD}-{HHMMSS}_{session-id}.md`
   - Without session ID: `_sessions/session-export-{YYYY-MM-DD}-{HHMMSS}.md`
5. Review the full conversation history carefully
6. Infer a short descriptive title for the session based on what was discussed
7. Write the export file following the format below
8. Write a companion compact instructions file (see Compact Instructions File)
9. Output the compact instructions to the terminal as well
10. Confirm to the user that the export and compact file were written

## Export File Format

```
# Session Export: {inferred-title}

**Exported:** {timestamp}
**Working Directory:** {cwd}
**Session ID:** {session-id or "N/A"}

---

## Executive Summary
[7-10 sentences summarizing the full session: what was the goal, what was accomplished, what is the current state]

## Decisions & Rationale
- **[Decision]:** [What was chosen] -- *Why:* [reasoning, tradeoffs considered]
[repeat for each significant decision; omit section if none]

## Dead Ends & Pivots
- **[Approach tried]:** [Why it failed or was abandoned] -- *Pivoted to:* [what replaced it]
[omit section entirely if none occurred]

## Prompt History

### Prompt N: [brief title]
**Goal:** [what the user asked for]
**Actions:** [what was done -- files read, created, modified, commands run]
**Outcome:** [result -- what worked, what didn't, what was learned]
**Status:** Completed | Interrupted | In Progress

[repeat for every prompt in the conversation]

## Files Changed
- `path/to/file` -- [created|modified|deleted]: [one-line description of change]
[repeat for each file; omit section if no files were changed]

## Open Questions & Blockers
- [Any unresolved issues, pending decisions, or items needing follow-up]
[omit section if none]

## Resume Context
[A dense 5-8 sentence paragraph written FOR Claude, containing everything needed
to continue this work after compaction or in a new conversation. Include: current
goal, what has been done so far, what is in progress, key files involved, architectural
context, and the immediate next step. This must be self-contained -- assume the reader
has zero prior context.]
```

## Compact Instructions File

After writing the export file, write a companion file at `{export-filepath}.compact.md` containing:

```
/compact Preserve session context from {export-filename}. Focus on the most recent Resume Context: {paste the Resume Context paragraph you just wrote}
```

This file sits alongside the export (e.g., `_sessions/session-export-2026-03-06-143000_abc123.md.compact.md`) so the compact instructions are always on disk and easy to find.

## Post-Export Output

After writing both files, output to the terminal:

```
Session exported to: {export-filepath}
Compact instructions saved to: {export-filepath}.compact.md

To compact now with full context, use:
/compact Preserve session context from {export-filename}. Focus on the most recent Resume Context: {resume context}
```

## Post-Compaction Re-orientation

If Claude detects it has just been compacted and an export file exists in `_sessions/`:
1. Read the most recent export file
2. Find the Resume Context section
3. Announce: "Resumed from session export. {one-line summary}. Ready to continue."

## Notes
- This command does NOT require `/session` to have been run first
- Use this as an emergency pre-compact export or as a standalone session documenter
- The `_sessions/` directory can be added to `.gitignore` if you don't want exports in version control
- If an active session IS stored in memory, mention it in the export header and suggest the user also run `/session-snapshot` for the tracked session file
- The companion `.compact.md` file is overwritten each time -- it always reflects the latest export
