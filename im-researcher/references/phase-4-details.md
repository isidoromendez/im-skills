# Phase 4 — Data Synthesis

Only read this file when `current_phase == "data_synthesis"`.
Requires `research_wiki.md` (Phase 3) and `scope_requirements.md` (Phase 1) as inputs, both from this same iteration (`iter-{n}`) — read them before starting.

## Goal

Consolidate findings from the wiki into conclusions and/or the final deliverable of this iteration (report, executive summary, recommendation), explicitly verifying that it responds to the requirements defined in Phase 1 of this same iteration. This document is also the mandatory input for Phase 1 of the next global iteration (if the user decides to start one).

## Flow

1. Review `scope_requirements.md` (for this iteration) and confirm that every research question and success criterion has an answer in the wiki. If something remains uncovered, point it out to the user instead of filling it in with assumptions.
2. If `global_iteration > 0`: also review the previous synthesis input (the synthesis of `iter-{N-1}`) and explicitly state in the new synthesis what was confirmed, what was corrected/updated, and what was added relative to that previous version — do not rewrite the previous synthesis from scratch as if it didn't exist.
3. Write the synthesis in the format the user prefers (narrative report, executive summary, list of recommendations, etc. — ask if it was not defined in Phase 1).
4. Cite or reference where each relevant conclusion comes from (wiki / sources of this iteration, and previous synthesis if applicable), without needing to repeat the complete details.
5. Iterate the synthesis with the user as many times as requested.

## Deliverable of the Phase

`final_synthesis`, saved at `/research-lc-workflow/{project_name}/iter-{n}/final_synthesis.md`.

## Before Requesting the Gate

Confirm with the user that the final synthesis answers the success criteria defined in Phase 1 of this iteration.

## Upon Gate Approval

`current_phase` transitions to `"finalized"` and this global iteration is considered closed. This triggers the global iteration closure procedure described in `SKILL.md` Section 4bis:
- Append `RESEARCH_CLOSED` in `iter-{n}/audit.md`.
- Append `GLOBAL_ITERATION_CLOSED` in `global_history.md`, citing the path of this `final_synthesis.md`.
- Update `project.json` (state of this iteration = `"finalized"`).
- Explicitly ask the user if they want to start a new global iteration (which will use this synthesis as input) or leave the research closed. Never start `iter-{n+1}` without being requested.
