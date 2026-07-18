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

## Step 5: Report, don't just "mark done"

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
```

Only treat a story as genuinely ready for `ccgs-story-done` once every criterion is a clear PASS or the user has explicitly accepted a documented deviation.
