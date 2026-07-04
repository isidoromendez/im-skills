# Audit Trail — {project_name} / iter-{n}

> This file is append-only and is specific to this global iteration. Never edit or delete an existing entry; each new event is appended at the end with its timestamp in ISO 8601 format (UTC or with explicit offset). Project-level events (start/closure of global iterations) go in `global_history.md`, not here.
>
> **Note:** The initial entry in this file differs depending on the case:
> in `iter-0` it is `RESEARCH_INITIATED` (as seen below). In `iter-N` with `N>0`, replace that first entry with a `GLOBAL_ITERATION_STARTED` event citing the path to `iter-{N-1}/final_synthesis.md` used as input.

---

## [__PENDING_TIMESTAMP__] — RESEARCH_INITIATED

- **Phase / Global Iteration:** iter-0 / mapping_and_scope
- **Event:** Initialization of the research workflow.
- **Details:** Initial state created in `/research-lc-workflow/{project_name}/iter-0/research_state.json`. Topic: "REPLACE".
- **Approved by User:** N/A (system event)

---
