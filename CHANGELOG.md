# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.3.0] - 2026-10-04

### Added

- `ccgs-save-session`: writes `production/session-state/active.md` with active story, what worked (with evidence), what failed and why, files state, decisions not yet in a GDD/ADR, blockers, and exact next step. Archives the previous snapshot to `production/session-state/archive/` and carries forward blocks appended by other skills (such as `ccgs-story-done` session extracts). Adapted from ECC `/save-session` (github.com/affaan-m/ECC, MIT).
- `ccgs-resume-session`: read-only loader for that snapshot. Verifies it against files on disk, story status, sprint plan, and recorded GDD/ADR decisions, lists relevant lessons, and gives a fixed-format briefing including "what not to retry" before doing any work. Adapted from ECC `/resume-session`.
- `ccgs-learn`: extracts one reusable lesson per run, routes design and architecture decisions to GDDs/ADRs instead of filing them, runs a Save / Improve then Save / Absorb / Drop gate, and writes `production/lessons/<slug>.md` plus `INDEX.md` after approval. Never edits skills, GDDs, or ADRs. Adapted from ECC `/learn-eval`.

### Changed

- `story-review-test-fix`: new Step 5, independent re-check from a fresh context. In `full` review mode a subagent receives only the story, GDD/ADR paths, changed files, and tests, writes its own criteria, and verdicts each one; disagreements are re-checked, capped at two rounds, then escalated. Former Step 5 (report) is now Step 6 and includes the re-check result. Pattern from ECC `santa-method` and fresh-context `code-reviewer`.
- README: skill count 21, new "Session & learning" group, session wrap-around in the pipeline section, project-folder files written by the new skills.

### Fixed

- Stale Claude Code paths in 11 skills (`ccgs-brainstorm`, `ccgs-consistency-check`, `ccgs-create-architecture`, `ccgs-create-epics`, `ccgs-design-system`, `ccgs-map-systems`, `ccgs-propagate-design-change`, `ccgs-reverse-document`, `ccgs-sprint-plan`, `ccgs-story-done`, `ccgs-story-readiness`), in both the unpacked folders and the `.skill` packages:
  - `.claude/docs/director-gates.md` → bundled `refs/director-gates.md`
  - `.claude/docs/templates/systems-index.md` → bundled `templates/systems-index.md` (`ccgs-map-systems` could not locate its template before)
  - `.claude/docs/technical-preferences.md` → `docs/architecture/architecture.md` or the project's technical preferences doc, if one exists

### Not adopted from ECC

- `continuous-learning-v2`, `strategic-compact`: depend on Claude Code hooks and an observer process.
- `verification-loop`: npm / tsc / pyright oriented.
- `csharp-testing`: xUnit and .NET, not Unity Test Framework.
- `skill-comply`: requires `claude -p` and `uv`.
- `architecture-decision-records`: already covered by `ccgs-create-architecture`.

### Notes

- `/create-stories` still has no dedicated skill. ECC's `epic-decompose` targets GitHub issues and was not a fit.

## [0.2.0] - 2026-07-21

### Added

- `ccgs-map-systems`: decomposes a game concept into individual systems, maps dependencies, prioritizes design order, and writes the systems index consumed by `/design-system`.
- `ccgs-consistency-check`: grep-first scan of all GDDs against the entity registry to catch cross-document drift (same entity/item/formula/constant with conflicting values); intended to run after each new GDD and before `/create-architecture`.
- `ccgs-story-readiness`: validates a story file is implementation-ready before dev starts, checking embedded GDD requirements, ADR references, and acceptance criteria; produces a READY / NEEDS WORK / BLOCKED verdict.
- `ccgs-propagate-design-change`: after a GDD is revised, scans ADRs and the traceability index for architectural decisions that may now be stale, and produces a change-impact report.
- `ccgs-reverse-document`: generates a GDD section, ADR, or concept doc by working backwards from existing code or a prototype, for undocumented features or inherited code.
- `unity-bug-root-cause` added to the repo: traces a Unity gameplay bug back to its originating decision point rather than patching the symptom (previously drafted locally, not yet committed).
- `UPGRADING.md`: version-detection instructions and three upgrade strategies (git remote merge, cherry-pick, manual `.skill` install).
- `CHANGELOG.md`: this file.

### Changed

- `README.md` rewritten from informal project notes into a structured README: pipeline diagram, skill inventory table, differences from the upstream Claude Code Game Studios project, and design philosophy section.
- Skill count badge updated to reflect the actual count in this repo (18).

### Notes

- `ccgs-map-systems/templates/systems-index.md` and the three templates under `ccgs-reverse-document/templates/` are newly authored in this release (no bundled template existed upstream for these). Marked "DRAFT TEMPLATE, needs human review" in-file; review before relying on them in production.
- The pipeline step `/create-stories` (referenced by `ccgs-create-epics` and by the pipeline diagram in the README) has no dedicated skill file in this repo. This is a pre-existing gap, not introduced by this release.

## [0.1.0] - unreleased baseline

Initial skill set prior to this changelog's existence: `ccgs-brainstorm`, `ccgs-code-review`, `ccgs-create-architecture`, `ccgs-create-epics`, `ccgs-design-system`, `ccgs-sprint-plan`, `ccgs-start`, `ccgs-story-done`, `query-me` (packaged `.skill` files), plus `story-review-test-fix`, `unity-clean-architecture-review`, `unity-tdd-workflow` (unpacked, generalized Unity engineering skills). No git tag exists for this baseline; it is reconstructed here from repository history for reference.
