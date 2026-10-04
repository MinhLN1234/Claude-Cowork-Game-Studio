# Claude Cowork Game Studio

A skills-only pipeline for solo/small-team Unity game development on Claude Cowork

![skills](https://img.shields.io/badge/skills-21-brightgreen) ![engine](https://img.shields.io/badge/engine-Unity-black) ![built for](https://img.shields.io/badge/built%20for-Claude%20Cowork-orange)

## Why This Exists

Building a game solo or in a small team with AI help is powerful and undisciplined at the same time. It is easy to hardcode a magic number instead of pulling it from a design doc, easy to skip writing the GDD and go straight to code, easy to end up with spaghetti systems that nobody planned, and easy to never ask "does this still match the vision?" until the drift has piled up across dozens of files.

This skill set imposes a professional studio process on a Claude Cowork session. Each phase (concept, systems design, architecture, stories, implementation, review) has a gate before the next one starts. Numeric values, entity definitions, and architectural decisions are checked against the actual design docs and codebase, not against memory. The goal is to catch drift while it is still one conflicting sentence in one file, not after it has been copied into ten.

## What's Included

21 skills, grouped by pipeline stage. Every skill bundles its own `roles/`, `refs/`, and `templates/` folders where needed, so there is nothing else to install: no separate subagents, no hooks configuration.

| Group | Skills |
| ---- | ---- |
| Design pipeline | `ccgs-start`, `ccgs-brainstorm`, `ccgs-map-systems`, `ccgs-design-system` |
| Architecture & consistency | `ccgs-create-architecture`, `ccgs-consistency-check`, `ccgs-propagate-design-change` |
| Stories & sprints | `ccgs-create-epics`, `ccgs-sprint-plan`, `ccgs-story-readiness` |
| Reviews & completion | `ccgs-code-review`, `ccgs-story-done`, `story-review-test-fix` |
| Reverse engineering | `ccgs-reverse-document` |
| Session & learning | `ccgs-save-session`, `ccgs-resume-session`, `ccgs-learn` |
| Unity engineering | `unity-tdd-workflow`, `unity-clean-architecture-review`, `unity-bug-root-cause` |
| Meta | `query-me` |

## The Pipeline

```
/start -> /brainstorm -> /map-systems -> /design-system -> /consistency-check -> /create-architecture
       -> /create-epics -> /create-stories -> /story-readiness -> /dev-story -> /code-review -> /story-done
```

`/propagate-design-change` and `/reverse-document` are used out of band, whenever they're needed rather than as a fixed step.

Every work session is wrapped by `/resume-session` at the start and `/save-session` at the end; `/learn` runs after a session that solved something worth keeping:

```
/resume-session -> [any pipeline step] -> /learn (optional) -> /save-session
```

- `/start`: first-time onboarding, figures out where you are and routes you to the right skill.
- `/brainstorm`: guided ideation using MDA, player psychology, and verb-first design; produces a game concept doc.
- `/map-systems`: decomposes the concept into individual systems, maps dependencies, and sets design priority order.
- `/design-system`: authors a full GDD for one system, section by section, cross-referencing dependencies as it goes.
- `/consistency-check`: scans all GDDs against the entity registry for drift (same entity, different stats in two docs).
- `/create-architecture`: builds the technical architecture blueprint and the mandatory ADR list, validated against the pinned Unity version.
- `/create-epics`: turns approved GDDs and architecture into epics, one per architectural module.
- `/create-stories`: referenced by the pipeline as the next step after epics; this repo does not yet bundle a dedicated skill for it (a pre-existing gap, not part of this release).
- `/story-readiness`: checks a story file against its GDD requirements, ADR references, and acceptance criteria before dev starts; verdict is READY / NEEDS WORK / BLOCKED.
- `/dev-story` (via `unity-tdd-workflow`): implements a story test-first, in Unity EditMode tests where possible.
- `/code-review` (`ccgs-code-review`, `unity-clean-architecture-review`): checks coding standards, SOLID compliance, and Clean Architecture separation between MonoBehaviour and domain logic.
- `/story-done`: end-of-story review; verifies acceptance criteria, prompts a deeper check via `story-review-test-fix`, updates story status.
- `/propagate-design-change`: after a GDD is revised, scans ADRs and the traceability index for decisions that may now be stale.
- `/reverse-document`: generates a GDD section, ADR, or concept doc by working backwards from existing code or a prototype.
- `unity-bug-root-cause`: traces a gameplay bug back to its originating decision point instead of patching the symptom.
- `/save-session`: writes `production/session-state/active.md` (active story, what worked with evidence, what failed and why, decisions not yet in a GDD/ADR, exact next step) and archives the previous snapshot.
- `/resume-session`: loads that snapshot, checks it against files, story status, and sprint on disk, and briefs before any work starts, including what not to retry.
- `/learn`: extracts one reusable lesson from the session, routes design decisions to GDDs/ADRs instead, runs a Save / Absorb / Drop gate, and files it under `production/lessons/`.

## What's New in This Release

v0.3.0 borrows three patterns from [ECC](https://github.com/affaan-m/ECC) (MIT), rewritten for Cowork (no hooks, everything stored in the project folder), and fixes broken paths left over from the Claude Code version.

- `ccgs-save-session` and `ccgs-resume-session`: every skill's Cowork notes asked for `production/session-state/active.md` to be updated at the start and end of each session, but no skill did it. These two do. Adapted from ECC's `/save-session` and `/resume-session`.
- `ccgs-learn`: manual, gated version of ECC's `/learn-eval`. ECC's automatic instinct learning needs Claude Code hooks, which Cowork does not run.
- `story-review-test-fix`: new Step 5, an independent re-check by a subagent that sees only the story, GDD/ADR, and changed files, never the dev conversation. Runs in `full` review mode only. Pattern from ECC's `santa-method` and fresh-context `code-reviewer`.
- Path fix: 11 skills referenced `.claude/docs/director-gates.md`, `.claude/docs/templates/systems-index.md`, or `.claude/docs/technical-preferences.md`, which do not exist in Cowork. They now point to the bundled `refs/` and `templates/` folders. `ccgs-map-systems` could not find its systems-index template before this fix.

See `CHANGELOG.md` for v0.2.0 and earlier.

## Getting Started

This runs on **Claude Cowork**

1. Connect this repo's folder in Cowork.
2. Install the `.skill` files you want (each is a zip of a `ccgs-<name>/` or `<name>/` folder containing `SKILL.md` plus its bundled `refs/`, `roles/`, `templates/`).
3. Invoke a skill by name, e.g. "run brainstorm" or "/map-systems".

## Project Structure

`design/`, `docs/`, `production/`, and `src/` live in your connected project folder, not in this repo. This repo only holds the skills themselves:

```
ccgs-<name>/
  SKILL.md       # instructions, including the "Cowork Adaptation Notes" block
  refs/          # shared reference docs (e.g. director-gates.md)
  roles/         # role files adopted inline in place of Claude Code subagents
  templates/     # document templates the skill writes from
ccgs-<name>.skill  # the same folder, zipped, for installation
```

The session and learning skills write only inside your project folder:

```
production/session-state/active.md     # current snapshot (ccgs-save-session)
production/session-state/archive/      # superseded snapshots, never edited
production/lessons/<slug>.md           # one lesson per file (ccgs-learn)
production/lessons/INDEX.md            # one line per lesson
```

