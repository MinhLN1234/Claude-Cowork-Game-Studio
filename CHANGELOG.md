# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

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
