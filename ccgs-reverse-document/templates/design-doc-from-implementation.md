status: reverse-documented
source: [path analyzed, e.g. src/gameplay/combat/]
date: [today]
verified-by: [User name]

# [System Name] Design

> **Note**: This document was reverse-engineered from the existing implementation.
> It captures current behavior and clarified design intent. Some sections may be
> incomplete where implementation is partial or intent was unclear.

> **DRAFT TEMPLATE — needs human review.** Authored during the Cowork adaptation
> pass (2026-07-21) to fill a gap: the source skill referenced this file but it was
> not bundled. Matches the section flow described in `SKILL.md` Phase 5-7. Review
> and adjust before treating it as final.

---

## Overview

[1-2 sentence summary of what this system does, written from what the code
actually does — not aspirational.]

---

## Mechanics (as implemented)

| Mechanic | Behavior | Source (file/function) |
| ---- | ---- | ---- |
| [Mechanic name] | [What it does, observed from code] | [file:line or function name] |

---

## Formulas Discovered

| Output | Formula | Variables | Source |
| ---- | ---- | ---- | ---- |
| [Output name] | [formula as found in code] | [variable meanings] | [file:line] |

---

## Design Intent (clarified with user)

> Captured from Phase 3 clarifying questions — do not infer intent silently.

- **[Mechanic/resource/value]**: [Clarified reason it exists — pacing, resource
  management, core pillar, legacy, etc.]
- **[Mechanic/resource/value]**: [Clarified reason]

---

## Edge Cases

**Handled in code:**
- [Edge case] — [how it's handled]

**Not handled / gaps identified:**
- [Edge case not covered by current implementation]

---

## Additions Made During Documentation

[Anything added to the doc that wasn't explicit in code — e.g. edge cases the
author flagged as missing, clarified intent, naming cleanup.]

---

## Sections Marked as Incomplete

- [Section] — [why it's incomplete: partial implementation, unclear intent, etc.]

---

## Follow-Up Recommended

1. [e.g. Run /consistency-check against entity registry]
2. [e.g. Rebalance flagged scaling/formula]
3. [e.g. Create ADR for a related architectural decision via `/reverse-document architecture`]
4. [e.g. Extend this doc when [missing feature] is implemented]
