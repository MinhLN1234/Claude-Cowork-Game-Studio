# Upgrading

## Find Your Current Version

This repo did not tag releases before this upgrade guide existed. If you cloned it before 2026-07-21, you are on the untagged baseline, referred to here as `v0.1.0` (9 packaged `.skill` files: `ccgs-brainstorm`, `ccgs-code-review`, `ccgs-create-architecture`, `ccgs-create-epics`, `ccgs-design-system`, `ccgs-sprint-plan`, `ccgs-start`, `ccgs-story-done`, `query-me`, plus 3 unpacked Unity/engineering skills: `story-review-test-fix`, `unity-clean-architecture-review`, `unity-tdd-workflow`).

To check what you have:

```bash
git tag                          # lists any version tags
git log --oneline -10            # recent history
ls *.skill                       # packaged skills present
```

If there is no tag and no `CHANGELOG.md`, you are on the pre-`v0.1.0` baseline.

## Upgrade Strategies

Pick whichever fits how you got this repo.

**A. Git remote merge (recommended if you cloned this repo directly)**

```bash
git remote add ccgs-upstream https://github.com/MinhLN1234/Claude-Cowork-Game-Studio.git
git fetch ccgs-upstream
git merge ccgs-upstream/master
```

Resolve conflicts in `README.md` and `CHANGELOG.md` manually; everything else (new skill folders and `.skill` files) merges cleanly since nothing else touches those paths.

**B. Cherry-pick specific commits**

If you only want the new skills and not the README/CHANGELOG rewrite:

```bash
git log ccgs-upstream/master --oneline    # find the commit(s) that add the new skills
git cherry-pick <commit-sha>
```

**C. Install `.skill` files by hand (Cowork users, no git access to upstream)**

"Installing" in Cowork means dropping a `.skill` file into your connected skill folder, not merging a `.claude/` directory the way Claude Code plugins do. To pick up new skills without touching anything else:

1. Download or copy the `.skill` file(s) you want from this repo.
2. Drop them into the folder you have connected in Cowork.
3. If you also want the unpacked folder (for readability or editing), unzip the `.skill` file into a matching `<name>/` folder next to it.

## v0.1.0 -> v0.2.0

**Safe to drop in as new files (no merge needed):**

- `ccgs-map-systems/` and `ccgs-map-systems.skill`
- `ccgs-consistency-check/` and `ccgs-consistency-check.skill`
- `ccgs-story-readiness/` and `ccgs-story-readiness.skill`
- `ccgs-propagate-design-change/` and `ccgs-propagate-design-change.skill`
- `ccgs-reverse-document/` and `ccgs-reverse-document.skill`
- `unity-bug-root-cause/` (new folder; previously drafted but never committed)

**Needs manual merge (these files changed and may conflict with local edits):**

- `README.md`: fully rewritten for this release. If you customized the old README, diff before overwriting.
- `CHANGELOG.md` and `UPGRADING.md`: new in this release; no conflict unless you already created your own.
- `story-review-test-fix/SKILL.md`, `unity-clean-architecture-review/SKILL.md`, `unity-tdd-workflow/SKILL.md`: unchanged in this release, but if you already had local edits to these three, check they were not overwritten by a strategy-A merge.

**Known gap carried into this release, not introduced by it:** `ccgs-map-systems/templates/systems-index.md` and the three templates under `ccgs-reverse-document/templates/` were authored during this upgrade because no bundled template existed for them upstream. They are marked "DRAFT TEMPLATE, needs human review" in-file. Review them against your project's actual GDD conventions before treating them as final.
