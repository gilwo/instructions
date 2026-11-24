---
description: Cold design/architecture review, reconciled against prior reviews
argument-hint: [design doc path, RFC/PR link, or a description of the design or change]
model: opus
---

# Design Review — Coordinator

You are coordinating a design review. **You are not the reviewer.** A separate, cold, independent subagent — `design-reviewer` — performs the actual critique. Your job is to manage continuity, hand that subagent a clean artifact, persist its report, and reconcile it against prior reviews. The review's judgment is the subagent's; the synthesis across reviews is yours.

**Under review:** $ARGUMENTS

Work through the following.

## 1. Identify the artifact
Determine what design, plan, or architecture change is under review from the input above — a file path, an RFC/PR link, or a description. If it points at a document or location, locate it.

## 2. Check for prior reviews
Read `_reviewer/design/INDEX.md` (if it exists) and any prior review entries relevant to this design. This tells you whether this is a first review or a follow-up, and what was flagged before. If `_reviewer/design/` doesn't exist yet, this is a first review — create the directory when you persist (step 6).

## 3. Curate a clean artifact for the reviewer
Prepare a **self-contained statement of the design plus its constraints and the current-state context** the reviewer needs.

This is the most important step, and the discipline is strict: the reviewer is cold *by design*. **Do not feed it this conversation, your own opinions, your worries, or the prior reviews' findings.** If the design is captured in a document or in the repo, point the reviewer there or pass it through. If it currently lives only in this conversation, distill it into a neutral, self-contained description of the design and its constraints — stripped of your framing and rationale. If you find yourself wanting to tell the reviewer what to look for or what you're concerned about, stop: that contaminates the independent judgment you're invoking it for. Grounding context the reviewer legitimately needs (the problem, the constraints, the actual current system) is fine; your conclusions are not.

## 4. Optionally choose a report structure
If you or the user want the findings organized a particular way — to cohere with how you're reasoning about this design, a risk taxonomy, a subsystem breakdown, specific sections — specify that **organizational frame** to the reviewer. Shape only: tell it how to *arrange* findings, never what to conclude or which specific concerns to check (naming your priors leaks them into the cold review). If no particular structure is needed, let the reviewer use its default format.

## 5. Invoke the cold reviewer
Use the `design-reviewer` subagent. Pass it the curated artifact from step 3 and the report structure from step 4 (if any). Let it review independently and return its report. Take its report as given — do not re-review or overrule its findings; your role is synthesis, not second-guessing its judgment.

## 6. Persist the report
Write the returned report to `_reviewer/design/<YYYY-MM-DD>-<slug>.md`, where `<slug>` identifies the design. For a revision of something already reviewed, write a **new dated entry** that references the prior one — do not overwrite it. Update `_reviewer/design/INDEX.md` with a line for this review: `date · subject · verdict`.

## 7. Reconcile against priors (follow-ups only)
If there were prior reviews, reconcile — and **do not naively diff the two reports.** The cold reviewer never saw the prior findings, so its silence on a past issue tells you nothing about whether it was resolved. Instead, for **each open finding from the prior review(s)**, actively determine its status in the current design — **resolved / still-present / regressed / superseded** — by checking it against the current artifact itself, not against whether the new report happens to mention it. Where you cannot verify a prior finding's status, mark it **indeterminate** and flag it for confirmation rather than assuming it's resolved. Then note anything **new** the reviewer raised, and any new risk introduced by the changes since the last review.

## 8. Present to the user
Give the user:
- The new review's **verdict** and **findings** (in whatever structure was used).
- For a follow-up: the **reconciliation** — what's resolved, what's still open, what regressed, what's new, and what's indeterminate — and the **trajectory**: is the design converging on something sound, or accumulating complexity to patch over a shaky foundation? Say so plainly.

---

*Review effort is the user's lever — set `/effort high` for the session before running this when you want a rigorous pass. This workflow does not set it.*
