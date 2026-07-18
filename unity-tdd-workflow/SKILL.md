---
name: unity-tdd-workflow
description: Write new Unity gameplay scripts test-first (red-green-refactor), structuring code so the core rule logic (health, timers/cooldowns, scoring, spawn timing) can be exercised in EditMode tests without a running scene. Use this whenever the user asks to implement a new gameplay feature, add a gameplay script, or says things like "viết feature mới", "implement story X", "add a system for Y" — especially when no tests exist yet for that system. Pairs well with unity-clean-architecture-review for structuring the split between logic and MonoBehaviour.
---

# Unity TDD Workflow

Writing the test first forces a design decision up front: can this logic even be tested without a live Unity scene? If the answer is no, that's usually a sign the gameplay rule is too entangled with MonoBehaviour lifecycle methods, and untangling it now is cheaper than after the feature is "done" and three other systems depend on it.

## Before writing any test

Check what acceptance criteria already exist for this feature. If the project uses a story/GDD workflow (e.g. CCGS-style stories and design docs), read the relevant story file and GDD section first. Otherwise, use whatever spec exists — a design doc, a ticket, or a written description from the user. The tests should verify the actual documented behavior (exact numbers, exact timing windows), not a guessed approximation. If no spec exists yet for this feature, that's a signal to pause and clarify the rule before encoding it into a test.

## Red-Green-Refactor, adapted to Unity

1. **Red — write a failing EditMode test first.** Use Unity Test Framework (`UnityEngine.TestTools`, NUnit attributes). Write the test against the plain-C# logic class you're about to create (or the interface it should expose), not against a MonoBehaviour directly — MonoBehaviours can't be instantiated with `new` and are awkward to unit test. If the feature genuinely requires scene/frame behavior (coroutines, physics, `Update` timing), write a PlayMode test instead, but prefer pushing as much logic as possible into a plain class first.

2. **Green — write the minimum code to pass.** Don't pre-build the MonoBehaviour wrapper, the VFX hookup, or the camera integration yet — just make the rule logic correct and passing.

3. **Refactor — then wire it into the Unity-facing side.** Once the logic is correct and tested, build the thin MonoBehaviour/presentation layer that calls into it. This is also the point to apply the clean-architecture checks: the MonoBehaviour should be a thin adapter, not where the rule logic lives.

4. **Repeat per behavior, not per class.** Write one small test per rule or edge case (e.g. "the counter resets after the timeout window," "it does NOT reset mid-window," "health cannot go below zero") rather than one giant test trying to cover the whole class at once. Small tests make it obvious which specific rule broke later.

## Where things go

Check the project for an existing test folder (commonly `Assets/Tests/`, or a `*.Tests` assembly definition) and existing test file naming before creating a new one — match the existing convention rather than inventing a new structure. Put the plain-C# logic classes wherever this project already keeps engine-independent domain code (detect the convention from the codebase — it may be a `Core/`, `Domain/`, `Gameplay/`, or a named framework folder). If no test infrastructure exists yet in this Unity project, flag that explicitly rather than silently skipping tests.

## Output expectations

When you implement a feature under this skill, the deliverable is the test file(s) and the implementation together — not implementation with tests added as an afterthought. If the user only asks for the test or only for the implementation, do that, but mention what's missing on the other side.

## When NOT to force this

Pure visual/feel work (VFX timing tuned by eye, camera easing that's judged by playtesting rather than a number) doesn't benefit from unit tests — don't manufacture assertions for things that are properly judged by playing the game. Use this skill for the rule logic underneath, not for things design intentionally leaves to feel/iteration.
