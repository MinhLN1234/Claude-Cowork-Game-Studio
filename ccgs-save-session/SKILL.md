---
name: ccgs-save-session
description: "Save the current work session to production/session-state/active.md so the next Cowork session can resume with full context: active story, what worked with evidence, what failed and why, decisions, blockers, exact next step. Use at the end of a work session, before a long session gets too heavy, or when the user says 'save session', 'lưu session', 'chốt phiên', 'wrap up for today'."
---

## Cowork Notes (read first)

Adapted from ECC's `/save-session` command (github.com/affaan-m/ECC, MIT) for the CCGS pipeline on Claude Cowork. Written natively for Cowork, so there is nothing to substitute:

1. **Storage**: everything lives in the user's connected project folder, never in `~/.claude/`. The live file is `production/session-state/active.md`. Superseded snapshots go to `production/session-state/archive/`.
2. **Shared file**: other CCGS skills append blocks to `active.md` (for example `ccgs-story-done` appends `## Session Extract — /story-done [date]`, the `game-designer` role appends section progress). This skill must carry those blocks into the snapshot, never silently drop them.
3. **Pairs with**: `ccgs-resume-session` reads what this skill writes. The Cowork Adaptation Notes in every other CCGS skill ask for `active.md` to be updated at the start and end of each session; this skill and its pair are how that happens.
4. **Hooks**: none. This skill is invoked by hand.


# Save Session

Capture what happened in this session (what was built, what worked, what failed, what is left) so the next session starts from the real state instead of from memory. The file is written for a future session with zero memory of this one.

**Output:** an updated `production/session-state/active.md` plus an archived copy of the previous version.


## Phase 1: Gather Context

Collect, from the conversation and from the project folder, before drafting anything:

1. **Active story and sprint.** Read the existing `production/session-state/active.md` if it exists. Read the most recent file in `production/sprints/` and `production/sprint-status.yaml` if present. Identify which story or GDD section this session worked on.
2. **Files touched.** List every file created or edited this session. Read each one's current state rather than recalling it; a file that was "almost done" an hour ago may have been changed since.
3. **Evidence.** For every claim that something works, find the evidence: a test that passed (name it), a value checked against a GDD section (cite it), a scene the user confirmed in Play mode. No evidence means it goes under "Not tried yet" or "In progress", not under "Worked".
4. **Failures.** Every approach that was tried and abandoned, with the exact reason (error message, wrong value, design conflict). This is the most important section: without it the next session will retry the same dead end.
5. **Decisions.** Design or architecture choices made in conversation that are not yet written into a GDD or ADR. Flag each one with where it should eventually live.
6. **Appended blocks.** Collect every `## Session Extract` or other block appended to `active.md` by other skills since the last snapshot.

If the session had no real work (only questions answered), say so and ask whether the user still wants a snapshot. Do not write an empty file.


## Phase 2: Draft the Snapshot

Draft the file in this exact format. Write every section. If a section genuinely has no content, write the stated fallback line; an honest empty section is better than a missing one.

```markdown
# Session State

**Last saved:** [YYYY-MM-DD HH:MM, user's local time]
**Project:** [project name from design/ or the folder name]
**Topic:** [one line: what this session was about]
**Active story:** [story file path and title, or "None (design work)" / "None"]
**Sprint:** [sprint file path, or "None"]
**Review mode:** [contents of production/review-mode.txt, or "lean (default)"]

---

## What We Are Building

[1 to 3 short paragraphs. The feature, system, or GDD section; why it matters; how it fits the systems index. Enough for someone with no memory of this session.]

## What Worked (with evidence)

- **[item]**: confirmed by [test name / GDD section and value / user confirmation in Play mode]

Fallback: "Nothing confirmed working yet."

## What Did NOT Work (and why)

- **[approach]**: failed because [exact reason or error]

Fallback: "No failed approaches this session."

## Not Tried Yet

- [concrete approach or idea, specific enough to act on]

Fallback: "No untried approaches identified."

## Current State of Files

| File | Status | Notes |
| ---- | ---- | ---- |
| `path/to/file` | Complete / In progress / Broken / Not started | [what is done, what is left] |

Fallback: "No files modified this session."

## Decisions Made (not yet in a GDD or ADR)

- **[decision]**: reason [why] → should be recorded in [GDD section / ADR / nowhere, conversational only]

Fallback: "No new decisions."

## Blockers and Open Questions

- [blocker or unanswered question, with who or what can unblock it]

Fallback: "No active blockers."

## Exact Next Step

[One concrete action, precise enough that resuming needs zero thinking about where to start. Include the file path and the skill to run if one applies, for example "Run ccgs-story-readiness on production/epics/combat/story-003-dash.md".]

Fallback: "Next step not determined. Review Not Tried Yet and Blockers before starting."

## Carried Forward Extracts

[Every block other skills appended since the last snapshot, copied verbatim, oldest first.]

Fallback: "None."
```


## Phase 3: Show, Confirm, Write

1. Show the full draft in the conversation.
2. Ask: "May I save this to `production/session-state/active.md`? The current version will be archived first." Wait for a yes. Apply any corrections the user gives and show the changed sections again.
3. On yes:
   - If `active.md` exists, copy it unchanged to `production/session-state/archive/[YYYY-MM-DD-HHMM].md` (create the folder if needed). Never append to an archived file and never overwrite one; if the name already exists, add `-2`, `-3`.
   - Write the new snapshot to `production/session-state/active.md`.
4. Re-read `active.md` and confirm every section header is present. If anything is missing, fix it before reporting.
5. Report: "Session saved. Previous state archived to [path]. Next session: run ccgs-resume-session."

If the user declines, write nothing. Verdict: **BLOCKED**, user declined write.


## Rules

- Evidence or it did not work. Do not move an item into "What Worked" because it looks right.
- Do not record a decision as made if the user only discussed it. "Leaning towards X" goes under Open Questions.
- Do not paste whole files or long logs into the snapshot. Point to the file path and summarize.
- Numeric values (damage, cooldowns, wave counts) written into the snapshot must match the GDD or be flagged as "not yet in GDD". The snapshot must not become a second source of truth for balance values.
- If the user asks to save mid-session, save what is known and mark in-progress items clearly. Saving more than once per session is fine; each save archives the previous snapshot.
