---
name: ccgs-resume-session
description: "Load production/session-state/active.md at the start of a work session, check it against the current sprint, stories and files on disk, and give a fixed-format briefing (state, what not to retry, blockers, exact next step) before doing any work. Use when starting a new Cowork session on an existing project, or when the user says 'resume', 'tiếp tục', 'làm tiếp hôm qua', 'where were we', 'continue from last session'."
---

## Cowork Notes (read first)

Adapted from ECC's `/resume-session` command (github.com/affaan-m/ECC, MIT) for the CCGS pipeline on Claude Cowork. Written natively for Cowork, so there is nothing to substitute:

1. **Storage**: reads `production/session-state/active.md` in the user's connected project folder. Older snapshots are in `production/session-state/archive/`. Never reads `~/.claude/`.
2. **Pairs with**: `ccgs-save-session`, which writes the snapshot this skill reads.
3. **Read-only**: this skill never edits `active.md` or any archived snapshot. The snapshot is a historical record; the next `ccgs-save-session` replaces it.
4. **Hooks**: none. This skill is invoked by hand.


# Resume Session

Load the last saved state, verify it against the project as it is now, and brief the user before touching anything. The point is to start from facts on disk, not from what the last session believed.


## Phase 1: Find the Snapshot

**If a path is given**, read exactly that file, even if a newer one exists.

**If a date is given** (`YYYY-MM-DD`), look in `production/session-state/archive/` for files starting with that date and pick the latest one. If none, say so and stop.

**If nothing is given:**

1. Read `production/session-state/active.md`.
2. If it does not exist, or it contains only `## Session Extract` blocks with no `# Session State` snapshot at the top (the project used other CCGS skills but never ran `ccgs-save-session`), say so plainly, show the most recent extract block if there is one, and offer to run `ccgs-start` or work from the latest sprint plan instead. Stop there.
3. If it is empty or only headings and placeholders, check `archive/` for the newest substantive file and tell the user which one you are loading instead.


## Phase 2: Read Everything, Then Verify Against Disk

Read the whole snapshot before summarizing. Then check it against the project as it is now:

1. **Files.** For every row in "Current State of Files", confirm the file exists. Missing file: `WARNING: [path] is in the snapshot but not on disk.`
2. **Story status.** If an active story is named, read the story file and `production/sprint-status.yaml`. If the status there differs from the snapshot (for example the snapshot says In progress but the story is now Complete), report the mismatch. The files on disk win.
3. **Sprint.** If a newer sprint file exists in `production/sprints/` than the one named in the snapshot, report it.
4. **Age.** If "Last saved" is more than 7 days ago: `WARNING: snapshot is [N] days old. Things may have changed.`
5. **Decisions not yet recorded.** For each item in "Decisions Made (not yet in a GDD or ADR)", grep the named GDD or ADR. If it has since been recorded, mark it resolved in the briefing. If not, keep it in the briefing as still pending.
6. **Carried extracts.** Any `## Session Extract` block appended below the snapshot after it was saved belongs to work done after the save. Include it in the briefing as newer than the snapshot.
7. **Lessons.** If `production/lessons/INDEX.md` exists, pick the lessons whose `applies-to` matches the system of the active story (or `all`). List at most 5 by slug and their "When to apply" line. Do not load their full text unless the next step needs it.


## Phase 3: Brief the User

Respond in exactly this format. Do not skip a section, even if it is empty.

```
SESSION LOADED: [path]
Saved: [date] ([N] days ago)

PROJECT: [project]  |  TOPIC: [topic]
ACTIVE STORY: [path and title, or None]

WHAT WE'RE BUILDING:
[2 to 3 sentences in your own words]

CURRENT STATE:
Working (with evidence): [count] items
In progress: [files]
Not started: [files]

WHAT NOT TO RETRY:
- [every failed approach and its reason, or "None recorded"]

DECISIONS STILL NOT IN A GDD/ADR:
- [decision → target doc, or "None"]

BLOCKERS / OPEN QUESTIONS:
- [items, or "None"]

DRIFT SINCE SAVE:
- [missing files, status mismatches, newer sprint, newer extracts, or "None"]

RELEVANT LESSONS:
- [slug: When to apply, or "None"]

NEXT STEP:
[exact next step from the snapshot, or "Not defined: pick from Not Tried Yet or Blockers"]
```

End with one line: "Ready to continue. Proceed with the next step?"


## Phase 4: Wait

Do not start work automatically. Do not edit any file.

- If the user says yes or continue and the next step is defined, do exactly that step, using the skill it names if any.
- If DRIFT SINCE SAVE listed anything that changes the next step (the story is already complete, the file is gone), say so and propose the corrected next step instead of following the stale one.
- If no next step is defined, ask where to start and suggest one item from "Not Tried Yet" or the next READY story from the current sprint.


## Rules

- "What Not To Retry" is always shown. It is the main reason this skill exists.
- Files on disk beat the snapshot. The snapshot beats memory.
- Balance values quoted in the snapshot are not authoritative. If the next step touches a numeric rule, read the value from the GDD, not from the snapshot.
- At the end of the resumed session, suggest running `ccgs-save-session` again.
