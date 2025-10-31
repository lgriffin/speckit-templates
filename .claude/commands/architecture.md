---
description: Generate or update architecture documentation with Mermaid diagrams from existing specification documents.
---

The user input to you can be provided directly by the agent or as a command argument - you **MUST** consider it before proceeding with the prompt (if not empty).

User input:

$ARGUMENTS

Given the optional context provided as an argument, do this:

1. Run `.specify/scripts/bash/setup-plan.sh --json` from the repo root and parse JSON for FEATURE_SPEC, SPECS_DIR, BRANCH. All future file paths must be absolute.
   - BEFORE proceeding, verify that at least plan.md and spec.md exist in SPECS_DIR. If missing, PAUSE and instruct the user to run `/plan` first, as architecture requires completed design artifacts.

2. Load and analyze available design documents from SPECS_DIR:
   - **Required**: spec.md (functional requirements, entities, user scenarios)
   - **Required**: plan.md (technical context, dependencies, project structure)
   - **Recommended**: data-model.md (entities, relationships, state transitions)
   - **Recommended**: research.md (technical decisions, integration patterns)
   - **Recommended**: quickstart.md (key user flows, test scenarios)
   - If any recommended documents are missing, WARN but continue with limited architecture view

3. Execute the architecture template:
   - Load `.specify/templates/architecture-template.md`
   - Set Input path to SPECS_DIR
   - Run the Execution Flow (main) function steps 1-11
   - The template is self-contained and executable
   - Follow validation and gate checks as specified

4. Generate all required Mermaid diagrams:
   - **System Context**: External actors, system boundary, data flows
   - **Component Architecture**: Layers, components, dependencies
   - **Data Model**: Entity relationships, state machines (if data-model.md exists)
   - **Key Flows**: 3-5 sequence diagrams from user scenarios
   - **Deployment Architecture**: Infrastructure, RBAC, configuration
   - **Technology Stack**: Languages, frameworks, dependencies

5. Write architecture documentation:
   - Write architecture.md to SPECS_DIR with all diagrams and sections
   - Include design principles and trade-offs
   - Add glossary of domain terms
   - Link references back to source documents
   - Update Execution Status tracking

6. Generate HTML companion file (optional but recommended):
   - Create architecture.html in SPECS_DIR
   - Embed Mermaid.js from CDN for guaranteed rendering
   - Apply styling for readability
   - Open in browser for immediate preview
   - This ensures diagrams render even without editor extensions

7. Validate architecture documentation:
   - All mandatory sections completed
   - All Mermaid diagrams render without errors (test syntax)
   - At least 3 key flows documented
   - Component names consistent across diagrams
   - No invented components (all extracted from specs)
   - Glossary defines domain terms
   - References link to all source documents

8. Report results:
   - File paths: architecture.md and architecture.html (if generated)
   - List of diagrams created
   - Any warnings about missing source documents
   - Confirmation that all diagrams validated successfully

Context for architecture generation: $ARGUMENTS

**Note**: This command can be run:
- After `/plan` completes to generate initial architecture
- Standalone to regenerate architecture after spec updates
- Multiple times to refine diagrams based on user feedback

Use absolute paths with the repository root for all file operations to avoid path issues.

