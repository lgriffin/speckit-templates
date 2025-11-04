---
description: Generate or update architecture diagrams and documentation
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

This command generates comprehensive architecture documentation with Mermaid diagrams based on the current feature's design artifacts.

1. **Setup**: Run `.specify/scripts/bash/check-prerequisites.sh --json` (or PowerShell equivalent) to get FEATURE_DIR and available documents.

2. **Load context**: Read available design documents from FEATURE_DIR:
   - spec.md (functional requirements, entities, user scenarios)
   - data-model.md (entities, relationships, state transitions)
   - research.md (technical decisions, integration patterns)
   - plan.md (technical context, dependencies, project structure)
   - quickstart.md (key user flows, test scenarios)

3. **Generate architecture documentation** with the following sections:

   ### System Context Diagram
   - Show the system and its external actors/systems
   - Use Mermaid C4 diagram or flowchart

   ### Component Architecture
   - Major system components and their relationships
   - Technology choices from research.md
   - Use Mermaid component or class diagram

   ### Data Model
   - Entity Relationship Diagram from data-model.md
   - State machines for entities with state transitions
   - Use Mermaid ER diagram and state diagrams

   ### Key Flows
   - 3-5 most important user flows from quickstart.md
   - Use Mermaid sequence diagrams

   ### Deployment Architecture
   - How the system is deployed
   - Based on technical context from plan.md
   - Use Mermaid deployment diagram

   ### Technology Stack
   - Layered view of technologies
   - From research.md and plan.md
   - Use Mermaid graph or flowchart

4. **Write architecture.md** to FEATURE_DIR with all generated diagrams and explanations.

5. **Report completion** with path to architecture.md and summary of generated diagrams.

## Key Rules

- Use absolute paths for all file operations
- Generate valid Mermaid diagram syntax
- Include textual explanations for each diagram
- Focus on clarity over complexity
- Ensure diagrams are technology-specific (unlike spec.md which is technology-agnostic)

