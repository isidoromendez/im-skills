---
name: phased-research-workflow
description: Executes a research workflow in four mandatory and iterable phases (Mapping and Scope, Sources Search, In-Depth Analysis, Data Synthesis), repeatable indefinitely as "global iterations" (iter-0, iter-1, ... iter-n) where each new loop starts from the final synthesis of the previous one. Persists state to disk via MCP filesystem under /research-lc-workflow/{project_name}/iter-N, with a detailed file per phase and strict human-in-the-loop validation gate control. ALWAYS use when asked to start, continue, resume, re-iterate, or audit a formal "investigation" or "research", or upon mention of `research-lc-workflow`, `research_state.json` (or `estado_investigacion.json`), "approval gate", "research sources", "new iteration", "iter-0/iter-n". Also activate when asked "what phase are we in", "approve to continue", "I want another iteration of this research", or when resuming a prior project by name. Do not use for one-off single-turn queries without state tracking.
---

# Phased Research Workflow (Gated Research Workflow)

Skill for conducting multi-stage research with persistent on-disk state, a mandatory project name, mandatory human approval between phases, a specific detail file per phase (progressive disclosure), and the ability to repeat the entire workflow indefinitely in chained "global iterations."

**Two levels of iteration — do not confuse:**
- **Phase iteration**: Adjustment rounds on the draft of a SINGLE phase before its approval gate is opened. Lives within `phases.<phase>.iterations` in `research_state.json` (see Section 3).
- **Global iteration**: A COMPLETE run of all four phases, from start to finish. Each global iteration lives in its own `iter-N/` directory and, starting from `iter-1`, begins using the `final_synthesis.md` from the previous global iteration as input (see Section 4bis).

**Portability requirement:** This skill is operating system agnostic. All disk operations must be performed exclusively using the MCP `filesystem` server tools (or an equivalent MCP server connected to the environment). **It is strictly forbidden to use shell/bash tools** to read, create, append, or modify any files for this workflow, or to obtain the date/time.

---

## 1. Master Directives (Agent's System Prompt)

1. **Mandatory project name, determined in Phase 1 of `iter-0`.**
   No research can start without a project name. If the user has not provided one, ask for it explicitly when starting `iter-0`. No files are created on disk until this name is defined. The project name is fixed for all future global iterations of that research.

2. **Fixed root path derived from the project name, with a subdirectory per global iteration:**
   ```
   /research-lc-workflow/{project_name_snake_case}/iter-{n}/
   ```
   where `n` starts at `0` and increments by 1 for each new global iteration (never reuse or skip numbers). See Section 2.4 for snake_case conversion, Section 2.5 for allowed directory resolution, and Section 4bis for the full global iteration cycle.

3. **Single source of truth, per iteration and at project level.** The state of a global iteration is ALWAYS `iter-N/research_state.json`. The global state of the project (how many iterations exist, which one is active) is ALWAYS `project.json` in the root of the project (outside any `iter-N/`). Neither is inferred from the conversation history.

4. **Four phases, strict order, within each global iteration:**
   - `mapping_and_scope`
   - `sources_search`
   - `in_depth_analysis`
   - `data_synthesis`

5. **The agent only loads the details of the active phase.** Each phase has its own reference file `references/phase-N-details.md` (bundled in this skill, not in the project folder). The agent does not read details of other phases, except to use the *deliverable* (not the instructions) of a previous phase as input for the active phase. This applies across any global iteration.

6. **Every phase is iterable before approval** (phase iteration, see terminology directive above and Section 3). The user decides how many adjustment rounds are needed.

7. **The entire workflow is repeatable indefinitely** (global iteration, see Section 4bis). Upon reaching `"completed"` for `data_synthesis`, the agent NEVER starts a new global iteration automatically — it offers it to the user and waits for them to explicitly request it.

8. **Human approval gate.** Upon finishing the deliverable of the active phase (to the user's satisfaction, not before), the agent sets `gate_pending: true`, stops, and waits for explicit approval. Ambiguous messages ("go ahead", "ok", "continue") are NEVER interpreted as approval. A request for more changes is a phase iteration, not a rejection; the gate simply hasn't opened yet.

9. **Never write phase or global iteration changes without recorded approval/confirmation.** `current_phase` only changes after an explicit approval is recorded in `iter-N/audit.md`. A new global iteration (`iter-{n+1}`) is only created after the user explicitly requests it, recorded in `global_history.md`.

10. **Every relevant milestone is audited** (append-only): at global iteration level in `iter-N/audit.md` (phase start, phase iteration, gate request, approval/rejection), and at project level in `global_history.md` (start/closure of each global iteration). See Sections 2.2 and 6.

11. **ISO 8601 timestamps, obtained without shell** (see Section 2.3).

12. **Context condensation when closing each phase and each global iteration, with a recommendation for a new conversation.** The agent cannot clear the current conversation history. What it must do instead:
    - Upon approving the gate of each phase: update `iter-N/condensed_context.md` (brief summary to resume this global iteration, see `assets/condensed_context_template.md`).
    - Upon closing a complete global iteration (Phase 4 approved): also append a summary of that iteration to `global_history.md` (what changed relative to the previous one, path to its `final_synthesis.md`).
    - In both cases, inform the user that they can open a new conversation to continue (next phase or next global iteration) without losing anything, as all necessary context is saved on disk. This is a recommendation, never an automatic action.

13. **Exclusive persistence on disk via MCP filesystem**, without relying on conversation memory or shell/bash commands.

---

## 2. Tool Declaration and Common Mechanics

### 2.1 Mapping operations to MCP tools

| Operation | MCP Tool to Use |
|---|---|
| Confirm allowed directories | `filesystem:list_allowed_directories` |
| Verify if project or iteration folder exists | `filesystem:list_directory` (of parent) or `filesystem:get_file_info` |
| Detect how many `iter-N/` directories exist | `filesystem:list_directory` on project folder (filter `iter-` prefix) |
| Create folders (project or `iter-N/`) | `filesystem:create_directory` (idempotent, creates parent dirs) |
| Read any project/iteration file or `references/phase-N-details.md` | `filesystem:read_text_file` |
| Create a file for the first time | `filesystem:write_file` (only if it doesn't exist) |
| Update specific fields of `research_state.json` or `project.json` | `filesystem:edit_file` (exact oldText/newText — never rewrite completely from memory) |
| Append a new entry to `audit.md` or `global_history.md` | Read-and-rewrite pattern (see 2.2) |
| Get real timestamp (ISO 8601) | See 2.3 — never shell |
| List directory contents | `filesystem:list_directory` |

If no MCP `filesystem` (or equivalent) is connected, alert the user and suggest enabling it — never use shell/bash as a fallback.

### 2.2 True Append-only without Shell

`filesystem:write_file` overwrites the complete file. To append to `audit.md` or `global_history.md`: (1) read the full file with `read_text_file`, (2) construct in memory `read_content + new_entry` (never the other way around), (3) `write_file` with the complete content. Never skip step 1.

### 2.3 Real Timestamp (ISO 8601) without Shell

1. Write the target file with a temporary placeholder `"__PENDING_TIMESTAMP__"` in the date field.
2. Call `filesystem:get_file_info` on that same file — its modification time reflects the actual write instant.
3. Replace the placeholder with that value using `filesystem:edit_file`.
4. Never invoke `date`, `Get-Date`, or any shell command; never invent the time from memory.

### 2.4 Project Name Normalization to snake_case

To construct `{project_name_snake_case}`: convert to lowercase, remove accents/diacritics, replace any sequence of spaces or non-alphanumeric characters with a single underscore `_`, and trim leading/trailing underscores. Example: "Impact of AI in Rural Education" → `impact_of_ai_in_rural_education`. Confirm the resulting name with the user before creating the directory. This name is set once in `iter-0` and is not requested again in subsequent global iterations of the same research.

### 2.5 Root Path Resolution

Before creating anything, call `filesystem:list_allowed_directories`:
- If `/research-lc-workflow` (or its parent) is among the allowed directories, use that absolute path as is.
- Otherwise, create `research-lc-workflow/{project_name_snake_case}` as a subdirectory within the first allowed directory and inform the user of the resulting absolute path.
- Never assume a path without having called `list_allowed_directories` at least once per session.

---

## 3. Work Cycle per Phase (with Phase Iteration) — Applies to all 4 phases within any iter-N

```
1. filesystem:list_allowed_directories (if not done yet this session)
2. filesystem:read_text_file on iter-N/research_state.json
   → confirm current_phase and gate_pending=false
3. Read ONLY the references/phase-<F>-details.md corresponding to current_phase
4. (If first time in this phase) append to iter-N/audit.md: PHASE_STARTED,
   update phases.<phase>.start + state="in_progress" (edit_file)
5. Produce/update the draft of the deliverable according to phase-<F>-details.md
   (in iter-0, phase 1, without previous inputs; in iter-N>0 phase 1, using
   final_synthesis.md from iter-{N-1} — see Section 4bis)
6. Present the draft to the user
7. If changes are requested (phase iteration):
     - Append ITERATION_REQUESTED to iter-N/audit.md (2.2)
     - Increment phases.<phase>.iterations (edit_file)
     - Go back to step 5. gate_pending remains false, current_phase does not change.
8. When the user is satisfied, the agent offers to request the gate (never assumes it).
9. Upon requesting the gate: timestamp (2.3), gate_pending=true,
   gate_requested_at=<timestamp> (edit_file), append GATE_REQUESTED (2.2).
10. STOP. Wait for explicit response.
11a. Approved → append USER_APPROVED (2.2); update (edit_file):
       phases.<phase>.end, approved_by_user=true, state="completed",
       gate_pending=false, current_phase=<next phase, or "finalized" if
       it was data_synthesis>; update iter-N/condensed_context.md
       (directive 12) and suggest a new conversation.
       If current_phase transitioned to "finalized" → see Section 4bis (global iteration closure).
11b. Asks for more changes → treat as phase iteration (go back to 7).
11c. Rejects general approach → append USER_REJECTED (2.2),
       gate_pending=false, do not advance phase, resume from step 5.
```

---

## 4. Initializing a New Research Project (Always creates `iter-0`)

1. If the user did not provide a project name, ask for it (directive 1).
2. Normalize to snake_case (2.4) and confirm with the user.
3. Call `filesystem:list_allowed_directories` and resolve the root path (2.5).
4. Call `filesystem:create_directory` on `/research-lc-workflow/{project_name_snake_case}/iter-0/`.
5. Create the following files with `filesystem:write_file` using the templates in `assets/`:
   - In the project root: `project.json` (from `project_template.json`, filling `project_name`, with `current_iteration: 0` and a single entry in `iterations`) and `global_history.md` (from `global_history_template.md`).
   - Inside `iter-0/`: `research_state.json` (from `research_state_template.json`, with `global_iteration: 0` and `previous_iteration_input: null`), `audit.md`, and `condensed_context.md`.
6. Resolve pending timestamps (2.3).
7. Confirm the created path to the user and start `mapping_and_scope` tasks by reading `references/phase-1-details.md`.

---

## 4bis. Global Iterations — Repeating the Entire Workflow

### When a New Global Iteration is Offered

Only when, within the active global iteration, the final gate of `data_synthesis` is approved (`current_phase` transitions to `"finalized"`). At that point, the agent:
1. Appends the event `RESEARCH_CLOSED` to `iter-N/audit.md`.
2. Appends a `GLOBAL_ITERATION_CLOSED` event to `global_history.md` (2.2), citing the path to `iter-N/final_synthesis.md`.
3. Updates `project.json`: the entry for this iteration transitions to `state: "finalized"`, with its `end` timestamp.
4. Explicitly asks the user if they want to start a new global iteration on this same research, or leave it closed. **Do not start `iter-{N+1}` unless the user explicitly requests it.**

The user can also request a new global iteration at any other time by saying something like "I want another iteration of this research" on a project whose last iteration is already finalized — the agent does not need to offer it first.

### How `iter-{n+1}` is Created

1. Read `project.json`, confirm that the current iteration is `"finalized"`, and determine `n+1` (the next consecutive integer; never reuse a previously used iteration number even if it was abandoned).
2. Call `filesystem:create_directory` on `/research-lc-workflow/{project_name}/iter-{n+1}/`.
3. Create the following files inside using the templates: `research_state.json` (with `global_iteration: n+1` and `previous_iteration_input` pointing to the path of `iter-{n}/final_synthesis.md`), `audit.md`, and `condensed_context.md`.
4. Append a `GLOBAL_ITERATION_STARTED` event to `iter-{n+1}/audit.md` citing the path of the previous synthesis used as a starting point.
5. Append the same event to `global_history.md` (2.2) at project level.
6. Update `project.json`: `current_iteration: n+1`, add the new entry to `iterations` with `state: "in_progress"`.
7. Start `mapping_and_scope` of `iter-{n+1}` by reading `references/phase-1-details.md`, which explicitly instructs how to use `final_synthesis.md` from `iter-{n}` as input for the new scope questions (do not ask for the project name again).

### What Does NOT Change Between Global Iterations

The project name and the root path of the project are fixed. Only the 4-phase cycle is repeated inside a new `iter-N/` directory.

---

## 5. Resuming an Existing Research Project

If the user mentions a project name that already exists under `/research-lc-workflow/`, or asks to "continue" without giving a name and only one project exists in that directory:

1. Read `project.json` to find `current_iteration` and its status — DO NOT assume anything from previous conversation history, especially if starting a new conversation. If `project.json` does not exist but `iter-N/` folders do, rebuild the index by listing the project directory (`filesystem:list_directory`) as a backup.
2. If the current iteration is NOT `"finalized"`: read `iter-{current_iteration}/research_state.json` and `condensed_context.md`, and resume the cycle in Section 3 from `current_phase`. If `gate_pending` is `true`, the first step is to resolve that gate, not to continue producing new content.
3. If the current iteration IS `"finalized"`: inform the user that this research already has a closed final synthesis, show its path, and ask if they want to start a new global iteration (Section 4bis) or just consult the existing findings.

---

## 6. Audit Entry Format

The same block format applies to `iter-N/audit.md` and `global_history.md`:

```markdown
## [__PENDING_TIMESTAMP__] — <EVENT_TYPE>

- **Phase / Global Iteration:** <current_phase or "iter-N">
- **Event:** <short description>
- **Details:** <what was done / what changes were requested / what the user replied>
- **Approved by User:** <true|false|N/A> — <short quote if applicable>

---
```

Event types in `iter-N/audit.md`: `RESEARCH_INITIATED` (iter-0 only), `GLOBAL_ITERATION_STARTED` (iter-N>0), `PHASE_STARTED`, `ITERATION_REQUESTED`, `GATE_REQUESTED`, `USER_APPROVED`, `USER_REJECTED`, `RESEARCH_CLOSED`.

Event types in `global_history.md`: `GLOBAL_ITERATION_STARTED`, `GLOBAL_ITERATION_CLOSED`.

---

## 7. Skill Reference Files and Templates

Phase details (read only the active phase):
- `references/phase-1-details.md` — Mapping and Scope
- `references/phase-2-details.md` — Sources Search
- `references/phase-3-details.md` — In-Depth Analysis
- `references/phase-4-details.md` — Data Synthesis

Project-level templates (root, outside any `iter-N/`):
- `assets/project_template.json`
- `assets/global_history_template.md`

Global iteration templates (inside each `iter-N/`):
- `assets/research_state_template.json`
- `assets/audit_template.md`
- `assets/condensed_context_template.md`
- `assets/research_sources_template.md` (Phase 2 deliverable)
- `assets/research_wiki_template.md` (Phase 3 deliverable)

---

## 8. Compliance Reminder

If the agent is tempted to: advance a phase or global iteration without explicit approval/request, interpret an ambiguous message as approval, limit how many phase iterations or global iterations the user "should" need, load details of inactive phases, use the shell "just this once", reuse an `iter-N` number already used, or forget to chain `final_synthesis.md` from the previous iteration when starting a new one — that is the signal to stop. None of those actions are permitted.
