Export the current conversation to a detailed session file without requiring a pre-initialized session. Use this when you need to preserve context before compacting or ending a conversation.

## Usage
```
/export-session [session-id] [reason]
```

## Parameters
- **session-id** (optional): A session ID to include in the filename. If omitted, checks memory for an active session ID first.
- **reason** (optional): A free-text statement of why you're exporting now. If omitted, the command will pause and ask for it (see Capturing Export Reason).

## Prerequisites
None -- this command works without `/session` initialization.

Steps:
1. Determine the current working directory
2. Check memory for an active session ID. If found, use it. If not, use the optional `session-id` parameter. Either or neither may be available.
3. Determine the **Export Reason** (see Capturing Export Reason below)
4. Create an `_sessions` directory in the current working directory if it doesn't exist
5. Generate a filename:
   - With session ID: `_sessions/session-export-{YYYY-MM-DD}-{HHMMSS}_{session-id}.md`
   - Without session ID: `_sessions/session-export-{YYYY-MM-DD}-{HHMMSS}.md`
6. Review the full conversation history carefully
7. Infer a short descriptive title for the session based on what was discussed
8. Write the export file following the format below
9. Write a companion compact instructions file (see Compact Instructions File)
10. Output the compact instructions to the terminal as well
11. Confirm to the user that the export and compact file were written

## Capturing Export Reason

- If a `reason` argument was provided inline, use it directly (do not ask).
- If no `reason` was provided, pause and ask the user: **"What's the reason for this export?"** Then wait for their reply.
- Skipping: a short sentinel reply (`-`, `.`, `n`, `no`, `skip`) means skip → record Export Reason as `(not provided)`. Any longer reply is treated as the reason text. (A truly empty Enter can't reach Claude, so a sentinel is required to skip.)
- Drift note: if a `Purpose` exists in memory for the active session AND the Export Reason clearly diverges from it, add a one-line italic note under the Export Reason: `_Drift from original purpose: {one-line description}_`. If no Purpose is in memory, omit the drift note entirely (a bare export with no active session simply records its reason with nothing to compare against).

## Export File Format

```
# Session Export: {inferred-title}

**Exported:** {timestamp}
**Working Directory:** {cwd}
**Session ID:** {session-id or "N/A"}

## Export Reason
{reason, or "(not provided)" if skipped}
{optional: _Drift from original purpose: ..._}

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
to continue this work after compaction or in a new conversation. LEAD with the goal:
open with the session Purpose (if known from memory) plus the Export Reason (e.g.,
"Goal: {Purpose}; exporting because {reason}."). If reason/purpose were skipped, fall
back to the auto-inferred goal as the opening instead -- do not write an empty "Goal:"
line. Then continue with: what has been done so far, what is in progress, key files
involved, architectural context, and the immediate next step. This must be
self-contained -- assume the reader has zero prior context.]
```

## Compact Instructions File

After writing the export file, write a companion file at `{export-filepath}.compact.md` containing:

```
/compact Preserve session context from {export-filename}. Focus on the most recent Resume Context: {paste the Resume Context paragraph you just wrote}
```

This file sits alongside the export (e.g., `_sessions/session-export-2026-03-06-143000_abc123.md.compact.md`) so the compact instructions are always on disk and easy to find.

Additionally, write (or overwrite) a per-session latest compact file:
- With session ID: `_sessions/latest_{session-id}.compact.md`
- Without session ID: `_sessions/latest.compact.md`

This file always reflects the most recent export for that session, so you can always find the latest compact instructions at a predictable path.

## Post-Export Output

After writing all files, output to the terminal:

```
Session exported to: {export-filepath}
Compact instructions saved to: {export-filepath}.compact.md
Latest compact: _sessions/latest_{session-id}.compact.md

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
