# Systems Index

*Created: [Date]*
*Status: [Draft / Under Review / Approved]*
*Last updated by: /map-systems*

> **DRAFT TEMPLATE — needs human review.** This template was authored during the
> Cowork adaptation pass (2026-07-21) to fill a gap: the source skill had no bundled
> template for this file. It follows the structural conventions used by the other
> bundled CCGS templates (`game-concept.md`, `game-design-document.md`), but has not
> been run against a real project yet. Review and adjust before treating it as final.

---

## Overview

| Field | Value |
| ---- | ---- |
| **Total systems** | [N] |
| **MVP systems** | [N] |
| **Vertical Slice systems** | [N] |
| **Alpha systems** | [N] |
| **Full Vision systems** | [N] |
| **Designed so far** | [N] / [Total] |

---

## System Enumeration

| System | Category | Description | Source (explicit / implicit) |
| ---- | ---- | ---- | ---- |
| [System name] | [Gameplay / UI / Meta / Technical] | [1-sentence description] | [Explicit — from concept / Implicit — inferred] |

---

## Dependency Map

Systems are grouped into layers. A system in a later layer depends on at least
one system in an earlier layer.

### Foundation (no dependencies)

- [System name]

### Core (depends only on Foundation)

- [System name] → depends on: [Foundation system(s)]

### Feature (depends on Core)

- [System name] → depends on: [Core system(s)]

### Presentation (UI/feedback wrapping gameplay systems)

- [System name] → depends on: [Gameplay system it presents]

### Polish (meta-systems, tutorials, analytics, accessibility)

- [System name] → depends on: [Systems it polishes]

### Circular Dependencies (flag and resolve)

- [System A] ↔ [System B] — **Resolution:** [interface abstraction / simultaneous design / contract definition]

### Bottleneck Systems (many others depend on these — high risk)

- [System name] — depended on by: [N] other systems

---

## Priority Assignment

| System | Priority Tier | Why |
| ---- | ---- | ---- |
| [System name] | [MVP / Vertical Slice / Alpha / Full Vision] | [Reasoning — mix technical necessity with player-experience impact; connect to a pillar or the core loop where possible] |

---

## Recommended Design Order

1. [System name] — [tier] — [why it's first]
2. [System name] — [tier]
3. ...

---

## Progress Tracker

| System | Status | GDD Path | Last Updated |
| ---- | ---- | ---- | ---- |
| [System name] | [Not Started / In Progress / Designed / Reviewed] | `design/gdd/[system].md` | [Date] |

---

## Director Notes

> **Creative Director Note** (if CD-SYSTEMS gate returned CONCERNS): [note]

> **Technical Director Note** (if TD-SYSTEM-BOUNDARY gate returned CONCERNS): [note]
