---
name: story-review-test-fix
description: >
  Verify that a finished story's implementation actually satisfies its acceptance criteria and GDD/epic spec, run or check its tests, and propose fixes for failures, without silently changing the spec to match the code. Use this as a deeper check alongside or before ccgs-story-done, such as when a story is marked ready for completion review, when tests are failing and need patching, or when the user asks "does this match the spec", "is this story actually done", or "why is this test failing." Distinct from unity-bug-root-cause (which traces unexplained bugs) — this skill specifically checks implementation against a written spec.
---

# Story Review + Test Fix

`ccgs-story-done` handles the completion ritual (status updates, surfacing the next story). This skill is the verification work underneath it: actually proving the code does what the story and GDD say it should, and fixing test failures in a way that respects the spec rather than bending the spec to whatever the code currently does.

## Step 1: Read the spec before the code

Pull the story file and trace its acceptance criteria back to the governing GDD section, epic, and any ADRs it cites. Write down each acceptance criterion as a separate, checkable statement before looking at the implementation — this avoids the common failure mode of skimming the code, deciding it "looks right," and retrofitting justification. If the project doesn't use formal story files, use whatever written spec exists (ticket, design doc, user's description) as the source of truth.

## Step 2: Map criteria to code, one at a time

For each acceptance criterion, find the specific code that's supposed to satisfy it. If you can't find code that addresses a criterion, that's a gap — don't assume it's handled elsewhere without checking. If a criterion is ambiguous enough that two different implementations would both seem to satisfy it, flag the ambiguity rather than picking an interpretation silently.

## Step 3: Run the tests, read the failures literally

When a test fails, read what it's actually asserting before deciding the test is "just out of date." Three possibilities, in order of how often each turns out to be true:

1. **The implementation is wrong** — it doesn't match the documented rule (check the GDD/design-doc value directly, not what the developer probably intended).
2. **The test is wrong** — it encodes an outdated or incorrect expectation. Verify against the spec before changing it; don't change a test just to make it pass.
3. **The spec itself is ambiguous or has changed** — in this case, don't unilaterally resolve it. Surface the discrepancy to the user explicitly; deviations from the GDD/ADRs should be flagged, not quietly absorbed into the code.

## Step 4: Fix at the right layer

If the fix touches a numeric rule or threshold (health, timing/cooldown, transition type, spawn/wave activation), check whether that value is referenced anywhere else — other scripts, other docs — and keep them in sync rather than fixing one occurrence. Fixing one place and missing a duplicate is a common way the same bug returns later.

## Step 5: Independent re-check from a fresh context (review mode `full` only)

The context that wrote or fixed the code shares its blind spots: it knows what the code was *meant* to do and reads that intent into what it actually does. In `full` review mode, get a second verdict from a context that has never seen this conversation. (Pattern adapted from ECC's `santa-method` and fresh-context `code-reviewer`, github.com/affaan-m/ECC, MIT.)

Resolve the review mode: `--review [full|lean|solo]` argument if given, else `production/review-mode.txt`, else `lean`. In `lean` or `solo`, skip this step and write "Independent re-check: skipped ([mode] mode)" in the report. The user can always ask for it explicitly in any mode.

In `full` mode:

1. Launch one general-purpose subagent via the Agent tool. Give it **only**:
   - the story file path and the governing GDD section and ADR paths from Step 1,
   - the list of files changed for this story (paths only; it reads them itself),
   - the test files for this story.
   Do **not** pass your criteria list, your Step 2 mapping, your Step 3 conclusions, or any summary of this conversation. Its value comes from not knowing them.
2. Brief it: "Read the story and its GDD/ADR references. Write down each acceptance criterion yourself. For each one, find the code and test that satisfy it and give PASS / FAIL / UNCLEAR with file:line evidence. Check every numeric value against the GDD, not against comments in the code. Do not edit any file."
3. Compare its verdicts with yours, criterion by criterion:
   - Both PASS: done.
   - Any disagreement, or any UNCLEAR: re-read the evidence yourself. If the subagent is right, treat it as a FAIL and go back to Step 3 or 4. If you are confident it is wrong, keep your verdict but list the disagreement in the report with both pieces of evidence, so the user decides.
   - A criterion the subagent found that you did not list in Step 1: you misread the story. Add it and check it.
4. Run at most two rounds (initial re-check, then one re-check after fixes). If they still disagree after that, stop and escalate to the user rather than looping.


## Step 6: Report, don't just "mark done"

Before handing off to `ccgs-story-done` for the completion ritual, give a clear pass/fail per acceptance criterion:

```
## Acceptance criteria check
- [criterion 1]: PASS / FAIL — [evidence: file/test/line]
- [criterion 2]: PASS / FAIL — [evidence]

## Test results
[which tests ran, which failed, root cause of each failure per Step 3's three categories]

## Spec deviations found
[anything where code and GDD/ADR disagree — flagged, not silently resolved]

## Fixes applied
[what was changed and why, including any other code/doc locations updated for consistency]

## Independent re-check
[full mode: agreed on N/M criteria; each disagreement with both verdicts and evidence, and how it was resolved. Otherwise: "skipped ([mode] mode)"]
```

Only treat a story as genuinely ready for `ccgs-story-done` once every criterion is a clear PASS or the user has explicitly accepted a documented deviation, and, in `full` mode, every re-check disagreement has been resolved or accepted by the user.
