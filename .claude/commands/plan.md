---
description: Execute the implementation planning workflow using the plan template to generate design artifacts.
---

The user input to you can be provided directly by the agent or as a command argument - you **MUST** consider it before proceeding with the prompt (if not empty).

User input:

$ARGUMENTS

Given the implementation details provided as an argument, do this:

1. Run `.specify/scripts/bash/setup-plan.sh --json` from the repo root and parse JSON for FEATURE_SPEC, IMPL_PLAN, SPECS_DIR, BRANCH. All future file paths must be absolute.
   - BEFORE proceeding, inspect FEATURE_SPEC for a `## Clarifications` section with at least one `Session` subheading. If missing or clearly ambiguous areas remain (vague adjectives, unresolved critical choices), PAUSE and instruct the user to run `/clarify` first to reduce rework. Only continue if: (a) Clarifications exist OR (b) an explicit user override is provided (e.g., "proceed without clarification"). Do not attempt to fabricate clarifications yourself.
2. Read and analyze the feature specification to understand:
   - The feature requirements and user stories
   - Functional and non-functional requirements
   - Success criteria and acceptance criteria
   - Any technical constraints or dependencies mentioned

3. Read the constitution at `.specify/memory/constitution.md` to understand constitutional requirements.

4. Execute the implementation plan template:
   - Load `.specify/templates/plan-template.md` (already copied to IMPL_PLAN path)
   - Set Input path to FEATURE_SPEC
   - Run the Execution Flow (main) function steps 1-9
   - The template is self-contained and executable
   - Follow error handling and gate checks as specified
   - Let the template guide artifact generation in $SPECS_DIR:
     * Phase 0 generates research.md
     * Phase 1 generates data-model.md, contracts/, quickstart.md
     * Phase 2 generates tasks.md
   - Incorporate user-provided details from arguments into Technical Context: $ARGUMENTS
   - Update Progress Tracking as you complete each phase

5. Generate architecture documentation:
   - Load `.specify/templates/architecture-template.md`
   - Execute the architecture template's Execution Flow (main) function steps 1-11
   - Input documents from $SPECS_DIR:
     * spec.md (functional requirements, entities, user scenarios)
     * data-model.md (entities, relationships, state transitions)
     * research.md (technical decisions, integration patterns)
     * plan.md (technical context, dependencies, project structure)
     * quickstart.md (key user flows, test scenarios)
   - Generate all required Mermaid diagrams:
     * System Context diagram
     * Component Architecture diagram
     * Data Model diagrams (ERD + state machines)
     * Key Flows sequence diagrams (3-5 flows)
     * Deployment Architecture diagram
     * Technology Stack diagram
   - Write architecture.md to $SPECS_DIR
   - Optionally: Generate architecture.html for browser viewing
   - Validate all Mermaid syntax renders correctly
   - Update architecture template's Execution Status

6. Verify execution completed:
   - Check Progress Tracking shows all phases complete
   - Ensure all required artifacts were generated (including architecture.md)
   - Confirm no ERROR states in execution
   - Validate architecture.md has all mandatory diagrams

7. Report results with branch name, file paths, and generated artifacts.

Use absolute paths with the repository root for all file operations to avoid path issues.
