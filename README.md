# Claude Cowork Game Studio

A skills-only pipeline for solo/small-team Unity game development on Claude Cowork, adapted from [Claude Code Game Studios](https://github.com/Donchitos/Claude-Code-Game-Studios).

![license](https://img.shields.io/badge/license-MIT-blue) ![skills](https://img.shields.io/badge/skills-18-brightgreen) ![engine](https://img.shields.io/badge/engine-Unity-black) ![built for](https://img.shields.io/badge/built%20for-Claude%20Cowork-orange)

## Why This Exists

Building a game solo or in a small team with AI help is powerful and undisciplined at the same time. It is easy to hardcode a magic number instead of pulling it from a design doc, easy to skip writing the GDD and go straight to code, easy to end up with spaghetti systems that nobody planned, and easy to never ask "does this still match the vision?" until the drift has piled up across dozens of files.

This skill set imposes a professional studio process on a Claude Cowork session. Each phase (concept, systems design, architecture, stories, implementation, review) has a gate before the next one starts. Numeric values, entity definitions, and architectural decisions are checked against the actual design docs and codebase, not against memory. The goal is to catch drift while it is still one conflicting sentence in one file, not after it has been copied into ten.

## What's Included

18 skills, grouped by pipeline stage. Every skill bundles its own `roles/`, `refs/`, and `templates/` folders where needed, so there is nothing else to install: no separate subagents, no hooks configuration.

| Group | Skills |
| ---- | ---- |
| Design pipeline | `ccgs-start`, `ccgs-brainstorm`, `ccgs-map-systems`, `ccgs-design-system` |
| Architecture & consistency | `ccgs-create-architecture`, `ccgs-consistency-check`, `ccgs-propagate-design-change` |
| Stories & sprints | `ccgs-create-epics`, `ccgs-sprint-plan`, `ccgs-story-readiness` |
| Reviews & completion | `ccgs-code-review`, `ccgs-story-done`, `story-review-test-fix` |
| Reverse engineering | `ccgs-reverse-document` |
| Unity engineering | `unity-tdd-workflow`, `unity-clean-architecture-review`, `unity-bug-root-cause` |
| Meta | `query-me` |

## The Pipeline

```
/start -> /brainstorm -> /map-systems -> /design-system -> /consistency-check -> /create-architecture
       -> /create-epics -> /create-stories -> /story-readiness -> /dev-story -> /code-review -> /story-done
```

`/propagate-design-change` and `/reverse-document` are used out of band, whenever they're needed rather than as a fixed step.

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

## What's New in This Release

- `ccgs-map-systems`: decompose a concept into systems, map dependencies, and produce the systems index that `/design-system` consumes.
- `ccgs-consistency-check`: grep-first drift check across all GDDs and the entity registry, meant to run after every new GDD.
- `ccgs-story-readiness`: pre-dev gate that verdicts a story READY / NEEDS WORK / BLOCKED before implementation starts.
- `ccgs-propagate-design-change`: change-impact scan that flags ADRs gone stale after a GDD edit.
- `ccgs-reverse-document`: builds a GDD, ADR, or concept doc backwards from existing code or a prototype, for undocumented features.
- Added `unity-bug-root-cause` to the repo (previously drafted but not committed), joining `unity-tdd-workflow` and `unity-clean-architecture-review` as generalized, engine-focused Unity skills usable independently of the CCGS pipeline.

Note: `ccgs-map-systems` and `ccgs-reverse-document` ship with newly authored template files (`templates/systems-index.md` for the former; three templates for the latter) that did not exist in the source material for this release. They are marked as draft in-file and should be reviewed before relying on them in production.

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

## Differences from Claude Code Game Studios

This is a direct adaptation, not a port with feature parity claims. Concretely:

- **Skills only, no subagents.** The original runs 49 subagents on Claude Code. This repo has none; each skill bundles the role files it needs in `roles/`, and Claude either spawns a general-purpose Cowork subagent with that role file prepended, or adopts the role inline.
- **No hooks.** The original enforces checks (JSON validation, naming conventions, no hardcoded magic numbers, session-state updates) via `settings.json` hooks. Cowork has no hook mechanism, so every skill's "Cowork Adaptation Notes" block says to do these checks manually.
- **Unity-first.** The original routes across Unity, Godot, and Unreal. This adaptation targets Unity (C#) only and ignores the other engine branches.
- **No path-scoped rules file.** The 11 rules from the original's rule engine are folded into each skill's own instructions rather than living in a separate rules system.
