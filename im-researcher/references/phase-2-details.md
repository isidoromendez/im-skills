# Phase 2 — Sources Search

Only read this file when `current_phase == "sources_search"`.
Requires `scope_requirements.md` (deliverable of Phase 1) as input — read it before proposing anything.

## Goal

Before reading or analyzing any content, decide with the user what types of sources will be searched and why. This phase is especially valuable when the user does not know in advance what material exists on the subject, or when that material is in other languages.

## Flow

1. **Propose and suggest.** Based on `scope_requirements.md`, propose concrete source categories and candidates: academic papers, specialized books, reference authors, recognized industry blogs, technical/institutional reports, regulations, or other categories that apply to the topic. Adjust the proposed mix to the topic and the languages available/accepted as defined in Phase 1.
2. **Validate with the user.** What source types to prioritize, which to exclude, preferences for language or specific authors/institutions, and whether there are sources the user already knows and wants to include.
3. **Compile the actual list.** Based on what was agreed, identify the specific sources (using web search if available) with their reference or link and a note explaining why each was selected.
4. **Iterate.** The list is adjusted with the user (adding, removing, reclassifying) as many times as necessary before requesting the gate.

## Deliverable of the Phase

`document_sources`, saved at `/research-lc-workflow/{project_name}/iter-{n}/research_sources.md`, using `assets/research_sources_template.md` of this skill as a base: list of sources by category, with link/reference and justification for each, plus validation notes (what was agreed, what was excluded and why).

This document is the primary input for Phase 3.

If `global_iteration > 0`, note that there may be sources already identified in `iter-{N-1}/research_sources.md` that remain valid for this run (especially if the Phase 1 scope of this iteration resumes previous questions). It is fine to offer the user to reuse/expand that list instead of rebuilding it from scratch, but the user decides, it is not assumed automatically.

## Before Requesting the Gate

Confirm with the user that the mix of source types and the concrete list reasonably cover the research questions from Phase 1, and that no source types the user considers important (e.g., sources in a specific language that haven't been searched yet) are missing.
