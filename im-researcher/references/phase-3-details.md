# Phase 3 — In-Depth Analysis

Only read this file when `current_phase == "in_depth_analysis"`.
Requires `research_sources.md` (deliverable of Phase 2) and `scope_requirements.md` (Phase 1) as inputs — read them before starting.

## Goal

Read the validated sources from Phase 2, extract all relevant information for the project (according to Phase 1 requirements), and organize it in a working wiki.

## Flow

1. Traverse the sources listed in `research_sources.md`, preferably grouped by research question/topic from Phase 1 (not necessarily in the same order they appear in the sources document).
2. For sources in another language, extract and translate the relevant information into the user's working language (unless requested otherwise), but preserve original technical terms where it adds precision.
3. Every finding must be traceable to its origin source (citation of the title/author as it appears in `research_sources.md`).
4. If any source proves not useful upon reading (irrelevant content, inaccessible, redundant), record this in the corresponding section of the wiki instead of silently omitting it — this prevents Phase 4 from searching for it again.
5. Iterate the wiki with the user: they may ask to deepen a topic, review an interpretation, or incorporate additional sources that have surfaced.

## Deliverable of the Phase

`research_wiki`, saved at `/research-lc-workflow/{project_name}/iter-{n}/research_wiki.md` (or, if the volume warrants it, multiple files in `research_wiki/<topic>.md` within a subdirectory of `iter-{n}/` — in that case, maintain an index in `research_wiki.md`), using `assets/research_wiki_template.md` as a base: findings organized by question/topic, with their source, and a brief synthesis per topic.

This document is the primary input for Phase 4.

If `global_iteration > 0`, the wiki of this iteration may refer specifically to findings already consolidated in `iter-{N-1}/research_wiki.md` when they remain valid, instead of reprocessing already covered sources — but the focus of this phase is the new or deepened information that motivated this iteration (see `scope_requirements.md` of this iteration).

## Before Requesting the Gate

Confirm with the user that the wiki covers all research questions from Phase 1 with sufficient evidence, and that there are no major gaps left unnoted.
