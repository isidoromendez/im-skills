# Phased Research Workflow Skill

To install this skill, run:

```bash
npx skills add isidoromendez/im-skills --skill phased-research-workflow
```

This skill implements a structured, state-persisted research workflow. It is designed to guide an AI agent through a rigorous four-phase research process with human-in-the-loop validation gates.

## Directory Structure

```
im-researcher/
├── SKILL.md                 # Core instructions and system directives
├── README.md                # This file
├── references/              # Step-by-step guidelines for each phase
│   ├── phase-1-details.md   # Phase 1: Mapping and Scope
│   ├── phase-2-details.md   # Phase 2: Sources Search
│   ├── phase-3-details.md   # Phase 3: In-Depth Analysis
│   └── phase-4-details.md   # Phase 4: Data Synthesis
└── assets/                  # Initial state templates (copied to target directories)
    ├── project_template.json
    ├── research_state_template.json
    ├── global_history_template.md
    ├── audit_template.md
    ├── condensed_context_template.md
    ├── research_sources_template.md
    └── research_wiki_template.md
```

## How It Works

1. **Strict Phased Progression**: The workflow must go through four consecutive phases:
   - **Mapping and Scope**: Align on motivation, research questions, limits, constraints, and success criteria.
   - **Sources Search**: Catalog and validate the target reference material.
   - **In-Depth Analysis**: Extract structured findings into an organized Wiki.
   - **Data Synthesis**: Produce the final report or executive summary.
2. **State Persistence**: State is stored on disk inside `/research-lc-workflow/{project_name}/` via the MCP filesystem.
3. **Approval Gates**: At the end of each phase, the agent requests explicit human confirmation to advance.
4. **Global Iterations**: Once a research run is finalized, the user can initiate a new global iteration starting from the previous synthesis.
