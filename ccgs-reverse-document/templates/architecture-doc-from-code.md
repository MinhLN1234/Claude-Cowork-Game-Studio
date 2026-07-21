status: reverse-documented
source: [path analyzed, e.g. src/core/entity-component/]
date: [today]
verified-by: [User name]

# ADR: [Decision Name]

> **Note**: This document was reverse-engineered from the existing implementation.
> It captures the pattern and technical decisions as found in code, plus intent
> clarified with the user. Some sections may be incomplete where the reasoning
> behind a decision could not be recovered.

> **DRAFT TEMPLATE — needs human review.** Authored during the Cowork adaptation
> pass (2026-07-21) to fill a gap: the source skill referenced this file but it was
> not bundled. Matches the section flow described in `SKILL.md` Phase 5-7, and the
> shape of ADRs referenced elsewhere in this skill set (`refs/director-gates.md`,
> `/create-architecture`). Review and adjust before treating it as final.

---

## Status

[Reverse-documented — existing code, not a new decision]

## Context

[What problem this part of the codebase solves. Written from what the code
actually does, not from aspirational architecture goals.]

---

## Pattern(s) Identified

| Pattern | Where Used | Evidence |
| ---- | ---- | ---- |
| [e.g. Singleton, Observer, Service Locator, ECS] | [file/module] | [file:line, or describe the structural evidence] |

---

## Technical Decisions (as implemented)

- **[Decision, e.g. threading model]**: [what the code does]
- **[Decision, e.g. serialization approach]**: [what the code does]

---

## Intent (clarified with user)

> Captured from Phase 3 clarifying questions — do not infer intent silently.

- **[Pattern/decision]**: [Clarified reason — testability, decoupling, performance,
  legacy/inherited, or other]

---

## Dependencies and Coupling

[What this module depends on, what depends on it, and how tightly coupled those
relationships are. Flag anything that looks like a coupling risk.]

---

## Performance Characteristics

[Any performance-relevant findings: allocation patterns, hot paths, known
bottlenecks — only include what's observed or has evidence, not guesses.]

---

## Constraints and Trade-offs

[What constraints (engine version, platform, legacy code) shaped this decision,
and what trade-offs were made as a result.]

---

## Gaps Identified

- [Aspect of the implementation where the reasoning could not be recovered, or
  where the pattern is inconsistently applied]

---

## Follow-Up Recommended

1. [e.g. Formalize this ADR's status once confirmed with the technical director]
2. [e.g. Address identified coupling risk in [module]]
3. [e.g. Extend to cover [related module] once analyzed]
