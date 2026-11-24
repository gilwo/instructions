---
description: Delegate a task to a subagent and orchestrate it — respawn on blocked, never pick up the work yourself
argument-hint: "<agent> <task>"
---

Delegate work to a subagent and orchestrate it. **You are the orchestrator, not the implementer** —
you do not perform the delegated work yourself, even if finishing it looks trivial.

- **Target agent:** the first word of `$ARGUMENTS` (e.g. `implementation`, `test`, `review`).
- **Task:** the rest of `$ARGUMENTS`.

Works with **any registered agent** — your own (`implementation`, `test`, …), built-in ones
(`general-purpose`, `Explore`, `Plan`), or any you add later; the loop keys off the *result*, not the
agent type. Blocked-detection is crispest when the agent reports it explicitly (a `Status: Blocked`
line); for agents that only describe the wall in prose, judge the state from their report. A finished
run that reports a negative/empty result (e.g. `Explore` finding nothing) is **DONE**, not blocked.

Apply this loop:

1. **Spawn** the target subagent with the task — clear scope, the relevant files/context, and an
   explicit definition of done.
2. Classify the result as **DONE** or **BLOCKED** (it reports `Status: Blocked` /
   `COULD NOT EXECUTE` / a blocked command, or returns partial work).
3. On **BLOCKED**, do **NOT** pick up the work (no editing the feature code or tests, no running its
   validation to "finish" it). Instead:
   - **Diagnose** the blocker (missing context? underspecified? permission/sandbox? missing dep/file?).
   - **Remediate the cause — orchestration only:** supply context/spec, fix/widen a permission,
     create a missing dir/config, narrow scope. (Unblocking ≠ doing the work.)
   - **Respawn the same subagent** with an augmented prompt carrying forward the prior blocker, what
     you changed, and any partial state (a fresh subagent remembers nothing).
   - **Budget: ≤ 2 respawns**, each changing something material.
4. If still blocked after the budget — or the only fix left is to do the work yourself — **STOP and
   escalate to the user** with the diagnosis and options.

If you catch yourself about to Edit/Write/validate the delegated artifact to "just finish it,"
that's the cue to respawn or escalate — not to proceed.
