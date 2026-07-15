I have just finished customizing a skill set for Claude, designed to enable solo game studio development. 
The core purpose of these skills is to fix the "vague brief → 60% quality output → endless back-and-forth" loop. 
The environment is built on Claude Projects (using "Claude Cowork"), functioning as a pipeline that runs from initial ideation to final code. 
I have already stress-tested this on 3–4 Unity/C# projects. 
The workflow includes: 
- Concept: /brainstorm guides ideation using professional frameworks (MDA, player psychology, verb-first design). Systems Design: /design-system drafts the GDD for each system, cross-references dependencies, and verifies global consistency. Architecture: /create-architecture constructs technical blueprints, generates a mandatory list of ADRs, and validates against the currently pinned engine version.
- Production: /create-epics → /create-stories → /sprint-plan, followed by a development loop utilizing /code-review and /story-done to close out each story according to its specific acceptance criteria. The strength of this setup lies not in the individual skills, but in how they are interconnected through a gated pipeline—each phase must be fully "matured" before the next can begin.
- I also implemented a "Query-Me" skill that sits at the front of the entire process.

This is a structured interrogation skill: it asks one question at a time, each accompanied by 2–3 options with clear consequences. It follows a dependency-based order to ensure core decisions are made first, followed by details. It forces the extraction of implicit logic, technical constraints, and design intent before any prototyping begins, while simultaneously performing continuous checkpoints against the brainstorm file to ensure context is never lost.
