---
name: unity-clean-architecture-review
description: Review Unity C# code for Clean Architecture and SOLID compliance, with specific attention to the separation between MonoBehaviour/presentation, plain-C# domain logic, and Unity/infrastructure concerns. Use this whenever the user asks to review, audit, or sanity-check a script or system before merging it, asks "is this code clean?", flags tight coupling or untestable code, or wants a second opinion on how a new gameplay system should be structured. Also trigger proactively when a user pastes a new MonoBehaviour and asks for feedback, even if they don't say "architecture" or "SOLID" explicitly.
---

# Unity Clean Architecture Review

Many Unity projects deliberately separate game logic from Unity's engine plumbing (often in a dedicated `Core/`, `Domain/`, or named framework folder). The point of that separation: gameplay rules (health math, cooldown/reset logic, spawn timing) should be testable and reasoned-about without booting the Unity engine, and should not silently drift from the values documented in the design docs. A review against this skill is checking whether a piece of code preserves that separation, not just whether it "looks clean."

## Before reviewing: look, don't assume

Read the actual code in the project first — how do existing systems separate domain logic from MonoBehaviour glue? Detect where this project keeps its engine-independent logic (it may be a `Core/`, `Domain/`, `Gameplay/`, or a named framework folder) and where its scene-facing scripts live. Conventions in a given codebase may not match textbook Clean Architecture layer names — infer the existing pattern from what's already there and review the new code against *that*, not against an abstract ideal. If the project has no such separation at all, say so and review against general SOLID principles instead.

## What to check

**Dependency direction.** Domain/logic classes (health, cooldowns, scoring, spawn rules) should not reference `UnityEngine` types they don't need (no `Update()`, no `GameObject`, no `Transform` inside pure rule logic). If a class needs engine services, it should receive them through an interface or a thin wrapper, not call `FindObjectOfType`, `GameObject.Find`, or static engine state directly from inside logic that's supposed to be testable.

**Single Responsibility.** A MonoBehaviour that reads input, mutates health, plays VFX, and updates the camera in one method is doing too much. Flag where these should split — input/presentation vs. rule evaluation vs. side effects (audio, VFX, camera).

**Testability.** Can this class's core behavior be exercised in an EditMode test without a scene? If not, identify exactly what's blocking that (singleton access, coroutine-only logic, hidden `Time.deltaTime` calls) and suggest the smallest change that would unblock it — don't propose a full rewrite for a one-line fix.

**Interface segregation / coupling.** Does this script depend on a fat manager class when it only needs one or two methods from it? Tight coupling here is usually what causes rule values to drift between code and docs — when logic is scattered and tangled, it's harder to find every place a rule lives.

**Consistency with design docs.** If the code implements a numeric rule (health thresholds, cooldown/reset timing, transition type, spawn/wave activation), cross-check the value against the project's design docs (GDD, project bible, or whatever spec the project keeps). A clean-architecture violation and a doc-drift bug often show up in the same review — flag both. If the project keeps no such docs, skip this check and note that.

## Output format

Structure findings as:

```
## Summary
[1-2 sentences: is this in good shape, needs minor cleanup, or needs restructuring?]

## Findings
- [File:line or method name] — [issue] — [why it matters] — [concrete suggested fix]

## Doc consistency
[Any numeric/rule values that should be cross-checked against design docs, or confirmation none were found / no docs exist]
```

Keep suggested fixes concrete and minimal — point at the smallest change that fixes the issue rather than redesigning the whole class, unless the user asks for a bigger refactor.
