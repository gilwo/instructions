# Claude Code Slash Commands

Custom slash commands for Claude Code session tracking and context preservation.

## Commands

### `/session`

Initialize a tracked session with a unique ID and title.

```
/session <session-id> <session-title>
```

- Creates a markdown file: `{Title}_{session-id}.md` in the working directory
- Stores session info in Claude's memory for the duration of the conversation
- Enables proactive snapshot reminders (every 3-5 prompts, at milestones, before compaction)

Get the session ID from `/status`.

### `/session-snapshot`

Capture a progress checkpoint and append it to the active session file.

```
/session-snapshot
```

Requires an active session (run `/session` first). Each snapshot includes:

- Executive summary
- Decisions & rationale
- Dead ends & pivots
- Per-prompt history (goal, actions, outcome, status)
- Files changed
- Open questions & blockers
- Resume context (dense paragraph for post-compaction re-orientation)

Also writes a companion `{session-file}.compact.md` with ready-to-use `/compact` instructions.

### `/export-session`

Export the full conversation to a standalone file. Works without `/session` initialization.

```
/export-session [session-id]
```

- If an active session exists in memory, uses that session ID automatically
- Otherwise accepts an optional session ID parameter
- Creates a timestamped file in `_sessions/` with the same sections as `/session-snapshot`
- Writes a companion `.compact.md` file

Use this as an emergency pre-compact export when you haven't initialized a tracked session.

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
| v3 (current) | Session-aware `/export-session`, companion `.compact.md` files for all commands |
