# Phase 1 — Mapping and Scope

Only read this file when `current_phase == "mapping_and_scope"`.

## Goal

Start from an idea (sometimes very raw, sometimes already refined by a previous global iteration) and, through an extensive cycle of high-value questions, reach a clear requirements document with the user. In `iter-0`, this phase is also responsible for setting the project name (see directive 1 in `SKILL.md`).

## Step 0 — Project Name (Blocking, `iter-0` only)

If `global_iteration == 0` and it is not yet defined, this is the first question of this phase. Without a project name, no files are created. In subsequent global iterations (`iter-N` with `N>0`), the name already exists — do not ask for it again.

## Step 0bis — If `global_iteration > 0`: Read the Previous Synthesis First

Before asking any questions, read with `filesystem:read_text_file` the file specified in `previous_iteration_input` of this iteration's `research_state.json` (the `final_synthesis.md` of `iter-{N-1}`). This reading is mandatory and replaces the need to review the entire conversation history of the previous iteration.

With that synthesis in hand, the questions in this phase should focus on:
- What parts of the previous synthesis the user considers resolved/closed (no need to investigate further on those).
- What they want to deepen, expand, update, or correct in this new run.
- Whether new research questions emerge that were not in the original scope.
- Whether any constraints or scope defined in `iter-0` change (language, type of sources, success criteria) or remain the same.

The scope document of this iteration (`scope_requirements.md`) must explicitly state, in its own section, what it takes from the previous synthesis as a starting point and what is new in this iteration.

## General Guide for High-Value Questions (Applies always, with or without previous iteration)

- Prioritize questions that reduce ambiguity the most, not trivial or filler questions.
- Go from general to specific:
  1. Motivation / real problem behind the idea — why this research (or this new run), what decision or need triggers it?
  2. Concrete objective — what do we want to be able to answer or produce at the end?
  3. Scope — what is explicitly in and what is explicitly out.
  4. Constraints — language(s) of sources, deadlines, expected depth (exploratory vs. exhaustive), acceptable or banned source types.
  5. Success criteria — how will the user know this iteration has met its goal.
- Do not force all questions into a single message if the user is still forming an idea. It is fine to iterate over several rounds within this phase (see phase iteration cycle in `SKILL.md` Section 3).
- Avoid unnecessary jargon; adapt the technical level of detail to how the user communicates.

## Deliverable of the Phase

A scope/requirements document, saved at `/research-lc-workflow/{project_name}/iter-{n}/scope_requirements.md`, containing at least:
- Topic and motivation
- Research objective (of this iteration)
- Concrete research questions
- Scope: what is covered and what is explicitly excluded
- Constraints (language, deadlines, source types, expected depth)
- Success criteria
- (Only if `global_iteration > 0`) What is carried over from the previous synthesis and what is new, referencing `iter-{N-1}/final_synthesis.md`

This document is the primary input for Phase 2 (to propose source types) and Phase 4 (to verify that the final synthesis of this iteration answers what was requested here).

## Before Requesting the Gate

Confirm with the user that the scope document accurately reflects what they want to research in this run (and, if applicable, that it captures what is carried over from the previous synthesis). In `iter-0`, also confirm that the project name and created folder look correct. Only then offer to request the gate (never assume it).
