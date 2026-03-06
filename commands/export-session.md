Export the current conversation to a detailed session file without requiring a pre-initialized session. Use this when you need to preserve context before compacting or ending a conversation.

## Usage
```
/export-session
```

## Parameters
None.

## Prerequisites
None -- this command works without `/session` initialization.

Steps:
1. Determine the current working directory
2. Create an `_sessions` directory in the current working directory if it doesn't exist
3. Generate a filename: `_sessions/session-export-{YYYY-MM-DD}-{HHMMSS}.md`
4. Review the full conversation history carefully
5. Infer a short descriptive title for the session based on what was discussed
6. Write the export file following the format below
7. Output a ready-to-use `/compact` command with the resume context baked in
8. Confirm to the user that the export was written and the compact command is ready

## Export File Format

```
# Session Export: {inferred-title}

**Exported:** {timestamp}
**Working Directory:** {cwd}

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

## Post-Export Compact Suggestion

After writing the file, output:

```
Session exported to: {filepath}

To compact now with full context, use:
/compact {paste the Resume Context paragraph you just wrote}
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
- If an active session IS stored in memory, mention it in the export and suggest the user also run `/session-snapshot` for that file
