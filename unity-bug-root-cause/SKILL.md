---
name: unity-bug-root-cause
description: Trace a Unity gameplay bug back to its original trigger instead of patching the symptom — use for issues that surface deep in execution, like health/HP desync, a timer or cooldown firing at the wrong moment, state or camera transitions glitching, spawns activating on the wrong wave/frame, or any behavior that diverges from what a design doc or GDD says. Trigger whenever the user describes a bug with "tại sao", "lỗi này do đâu", "sao nó lại", a stack trace, reproduction steps, or "X should happen but Y happens instead." Prefer this skill over jumping straight to a fix when the cause isn't obvious from the error site itself.
---

# Unity Bug Root Cause

The instinct when a bug report comes in is to fix it where it's visible — add a null check where the exception threw, clamp a value where it went negative. That often just hides the symptom one level up and the same bug resurfaces somewhere else. This skill is about tracing backwards through the actual causal chain until you find the original decision point that produced the wrong state, then fixing there.

## The class of bug this catches

A large share of "weird bug" reports in a gameplay codebase aren't logic errors at the symptom site — they're **drift**: a numeric rule or timing constant (health value, cooldown window, transition type, spawn/wave number) that no longer matches what the design docs say, or that's been duplicated and only updated in one place. Before assuming a logic bug, check whether the misbehaving value still matches its documented spec. This is often the fastest path to the real cause.

## Process

1. **Pin down the expected behavior first.** Before touching code, find what's *supposed* to happen. Look for the project's design docs — a GDD, a project bible / `CLAUDE.md`, or whatever spec the project keeps — and read the actual rule (exact value, timing window, transition type, wave/frame number). If the docs are ambiguous or silent, say so rather than guessing.

2. **Reproduce, don't assume.** Get exact repro steps or the exact moment in a playthrough where it goes wrong. If the report is vague ("the camera glitches sometimes"), ask for the specific trigger before tracing — root-causing a bug you can't pin down wastes time on the wrong branch.

3. **Walk backwards from the symptom.** Find where the wrong value/state is first *observed* (the crash, the visual glitch, the wrong number on screen). Then ask: where was this value last *correct*? Trace the call chain or event chain backwards between those two points — this is usually faster than reading forward from where you guess the bug "probably" is.

4. **Use the codebase as evidence, not memory.** Grep for the actual values and method names involved (the setters, the reset/trigger calls, the state transitions, the spawn logic) rather than reasoning from what the code "probably" does. Read the real call sites.

5. **Distinguish trigger from symptom.** The original trigger is usually further back than it looks — e.g. a counter resetting too early might be caused by an event firing twice, not by the reset logic itself being wrong. Keep asking "what caused this state to become true" until you hit something that's either (a) a single clear root cause, or (b) a doc/value mismatch.

6. **Check for the same drift elsewhere.** If the root cause turns out to be a stale or duplicated value (a threshold, a timing constant), grep the rest of the codebase *and* the docs for the same value — fixing it in one place and missing a duplicate is a common way this exact bug returns later.

## Output format

```
## Symptom
[what the user observed]

## Expected behavior (per docs)
[the documented rule, with doc filename/section, or "not documented" if so]

## Root cause
[the actual originating trigger — be specific: function, line, event]

## Why the symptom looks the way it does
[the causal chain from root cause to observed symptom]

## Fix
[where to fix — at the root cause, not the symptom site — and what else needs to stay in sync]

## Other places to check
[any other doc or code location referencing the same value/rule, if relevant]
```

If after tracing you genuinely can't isolate a single root cause (e.g. it's a race condition that needs more repro data), say that plainly and propose the next diagnostic step rather than guessing at a fix.
