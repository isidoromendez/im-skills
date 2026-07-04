# IM Skills Repository

This is a multi-skill repository containing custom agentic capabilities designed for integration with AI assistants. Each skill is self-contained under its own subdirectory, with assets, references, and executable instructions.

## Repository Structure

```
├── im-researcher/           # Phased Research Workflow Skill
│   ├── SKILL.md             # Core skill declaration and rules
│   ├── README.md            # Skill-specific documentation
│   ├── assets/              # File templates used by the skill
│   └── references/          # Phase-specific instructions and details
```

## Available Skills

### 1. [Phased Research Workflow (im-researcher)](file:///c:/Proyects/Skills/im-skills/im-researcher/README.md)
A structured, multi-phase research workflow with persistent disk state, project-scoped isolation, and mandatory human-in-the-loop validation gates.
- **Workflow Phases**:
  1. **Mapping and Scope**: Define motivation, goals, scope, and criteria.
  2. **Sources Search**: Propose and validate curated reference sources.
  3. **In-Depth Analysis**: Read sources, extract findings, and compile a research wiki.
  4. **Data Synthesis**: Consolidate findings into a final report/executive summary.
- **Key Features**: State persistence via MCP filesystem, append-only auditing, context condensation, and global iteration capabilities.
