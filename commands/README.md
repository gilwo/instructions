# Claude Code Slash Commands

Custom slash commands for Claude Code session tracking and context preservation.

## Commands

### `/session`

Initialize a tracked session with a unique ID and title.

```
/session <session-id> <session-title> [purpose]
```

- Creates a markdown file: `{Title}_{session-id}.md` in the working directory
- Captures a user-stated **Purpose** in the file header (and in memory). Provide it inline, or the command asks and waits for it
- Stores session info in Claude's memory for the duration of the conversation
- Enables proactive snapshot reminders (every 3-5 prompts, at milestones, before compaction)

Get the session ID from `/status`.

### `/session-snapshot`

Capture a progress checkpoint and append it to the active session file.

```
/session-snapshot [intent]
```

Requires an active session (run `/session` first). Each snapshot includes:

- **Intent** (user-stated reason for this checkpoint; provided inline or asked) — plus a `Drift from original purpose` note when it diverges from the session Purpose
- Executive summary
- Decisions & rationale
- Dead ends & pivots
- Per-prompt history (goal, actions, outcome, status)
- Files changed
- Open questions & blockers
- Resume context (leads with the goal — Purpose + latest Intent — for post-compaction re-orientation)

Also writes a companion `{session-file}.compact.md` with ready-to-use `/compact` instructions.

### `/export-session`

Export the full conversation to a standalone file. Works without `/session` initialization.

```
/export-session [session-id] [reason]
```

- If an active session exists in memory, uses that session ID automatically
- Otherwise accepts an optional session ID parameter
- Captures a user-stated **Export Reason** (provided inline or asked) in the export header
- Creates a timestamped file in `_sessions/` with the same sections as `/session-snapshot`
- Writes a companion `.compact.md` file (and a per-session `latest_{id}.compact.md`)

Use this as an emergency pre-compact export when you haven't initialized a tracked session.

## Capturing Intent

All three commands capture a user-stated intent layered on top of Claude's automatic summary:

- **Inline or ask:** pass the text as a trailing argument, or the command pauses and asks, then waits for your reply.
- **Skip:** reply with a short sentinel (`-`, `.`, `n`, `no`, `skip`) to record `(not provided)` and proceed with the auto-summary only. (A truly empty Enter can't reach Claude, so a sentinel is needed.)
- **Three altitudes:** `## Purpose` (original goal, frozen at `/session`), `### Intent` (per-snapshot, captures evolution/drift), `## Export Reason` (per-export). Together with the auto Executive Summary (what actually happened) they cover original goal vs. evolving goal vs. actual progress.

## Typical Workflow

```
1. /session <id> "Feature X"          # start tracking
2. (work normally for several prompts)
3. /session-snapshot                   # checkpoint
4. (more work)
5. /session-snapshot                   # another checkpoint
6. cat {file}.compact.md              # grab compact instructions
7. /compact <instructions>            # compact with full context
8. (Claude re-orients from session file automatically)
```

## Emergency Workflow

```
1. (deep in a session, no /session was run, hitting context limits)
2. /export-session                     # creates _sessions/ export + .compact.md
3. /compact <instructions from .compact.md>
```

## File Structure

```
project/
  {Title}_{session-id}.md              # session file (from /session)
  {Title}_{session-id}.md.compact.md   # latest compact instructions
  _sessions/
    session-export-{date}-{time}_{id}.md          # export file
    session-export-{date}-{time}_{id}.md.compact.md  # compact instructions
```

## Version History

| Version | Changes |
|---------|---------|
| v1 | Initial `/session` and `/session-snapshot` (simple executive summary + prompt history) |
| v2 | Rich snapshot format: decisions, dead ends, files changed, resume context, `/compact` integration |
| v3 | Session-aware `/export-session`, companion `.compact.md` files for all commands |
| v4 (current) | User-stated intent capture: `## Purpose` (/session), `### Intent` + drift note (/session-snapshot), `## Export Reason` (/export-session); goal-led Resume Context |
