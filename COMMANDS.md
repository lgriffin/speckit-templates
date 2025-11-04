# Spec-Kit Commands HOWTO

This document explains how custom commands work in this spec-kit implementation and how they align with the official [spec-kit workflow](https://github.com/github/spec-kit).

## Table of Contents

- [Overview](#overview)
- [How Command Registration Works](#how-command-registration-works)
- [Available Commands](#available-commands)
- [Typical Workflow](#typical-workflow)
- [Command Details](#command-details)
- [Permissions Model](#permissions-model)
- [How This Aligns with Spec-Kit](#how-this-aligns-with-spec-kit)
- [Extending Commands](#extending-commands)

---

## Overview

This repository uses **Cursor/Claude-specific custom commands** to implement the Spec-Kit workflow. Commands are registered in `.claude/settings.local.json` and executed by the AI agent when you type them in chat.

### Quick Start

```
/speckit.specify Create a todo app with user authentication
/speckit.clarify
/speckit.plan Use React with TypeScript, Express backend, PostgreSQL
/speckit.architecture
/speckit.tasks
/speckit.implement
```

---

## How Command Registration Works

### The Registration File

Commands are defined in `.claude/settings.local.json`:

```json
{
  "customInstructions": [
    {
      "name": "/speckit.plan",
      "path": ".claude/commands/plan.md"
    }
  ]
}
```

### Execution Flow

When you type `/speckit.plan Use React and Express`:

1. **Cursor reads** `.claude/settings.local.json`
2. **Finds** the command name `/speckit.plan`
3. **Loads** the instruction file `.claude/commands/plan.md`
4. **Injects** the markdown content as AI instructions
5. **Replaces** `$ARGUMENTS` with `Use React and Express`
6. **Executes** the AI with these specialized instructions

### Command Structure

Each command file (`.claude/commands/*.md`) contains:

```markdown
---
description: Brief description of what the command does
---

User input:

$ARGUMENTS

[Step-by-step instructions for the AI...]
```

The AI follows these instructions exactly, running bash scripts, reading templates, and generating artifacts.

---

## Available Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/speckit.specify` | Create initial feature specification | Start of new feature |
| `/speckit.clarify` | Ask clarifying questions about the spec | After `/specify`, before `/plan` |
| `/speckit.plan` | Generate implementation plan & architecture | After spec is clear |
| `/speckit.architecture` | Generate architecture diagrams | Part of `/plan` workflow |
| `/speckit.tasks` | Break plan into actionable tasks | After `/plan` completes |
| `/speckit.analyze` | Analyze existing code/specs | Any time you need analysis |
| `/speckit.implement` | Execute the task breakdown | After `/tasks` completes |
| `/speckit.constitution` | Review/update project constitution | Set project guidelines |

---

## Typical Workflow

### Phase 1: Specification (Discovery)

```bash
# 1. Create initial spec from natural language
/speckit.specify Create a real-time chat application with rooms, user presence, and message history

# 2. Clarify ambiguities
/speckit.clarify
# AI will ask targeted questions about unclear areas
# Answer them in the chat, AI updates spec.md

# Verify: Check .specify/specs/###-feature-name/spec.md
```

**Artifacts Created:**
- `.specify/specs/###-feature-name/spec.md` (feature specification)
- Git branch: `###-feature-name`

---

### Phase 2: Planning (Design)

```bash
# 3. Generate implementation plan
/speckit.plan We'll use Socket.io for WebSockets, React frontend, Node.js/Express backend, Redis for pub/sub, PostgreSQL for persistence

# AI will:
# - Read the spec
# - Research tech stack
# - Generate data models
# - Create API contracts
# - Design architecture
# - Create quickstart scenarios
```

**Artifacts Created:**
- `plan.md` - Implementation strategy
- `research.md` - Technical decisions
- `data-model.md` - Entities and relationships
- `contracts/` - API specifications
- `quickstart.md` - Test scenarios
- `architecture.md` - System diagrams

---

### Phase 3: Task Breakdown

```bash
# 4. Generate actionable tasks
/speckit.tasks

# AI will:
# - Parse the plan
# - Break into phases (Setup, Tests, Core, Integration, Polish)
# - Identify dependencies
# - Mark parallel tasks with [P]
```

**Artifacts Created:**
- `tasks.md` - Ordered, dependency-aware task list

---

### Phase 4: Implementation

```bash
# 5. Execute implementation
/speckit.implement

# AI will:
# - Read tasks.md
# - Execute tasks in dependency order
# - Run tests as specified
# - Handle errors and validate
```

**Result:**
- Working implementation following the plan

---

## Command Details

### `/speckit.specify [feature description]`

**Purpose:** Initialize a new feature specification from natural language.

**Usage:**
```bash
/speckit.specify Build a user profile editor with avatar upload, bio, and social links
```

**What it does:**
1. Runs `.specify/scripts/bash/create-new-feature.sh` to create branch and directory
2. Loads `.specify/templates/spec-template.md`
3. Writes `spec.md` with structured requirements
4. Reports branch name and next steps

**Next Step:** `/speckit.clarify`

---

### `/speckit.clarify`

**Purpose:** Identify and resolve ambiguities in the specification.

**Usage:**
```bash
/speckit.clarify
```

**What it does:**
1. Analyzes current spec for ambiguities
2. Asks up to 5 targeted clarification questions
3. Records answers in `spec.md` under `## Clarifications`
4. Can be run multiple times

**Best Practice:** Always run before `/plan` to reduce rework.

**Next Step:** `/speckit.plan`

---

### `/speckit.plan [technical details]`

**Purpose:** Generate comprehensive implementation plan with architecture.

**Usage:**
```bash
/speckit.plan Use Python 3.11 with FastAPI, PostgreSQL database, Docker for deployment
```

**What it does:**
1. Checks for clarifications (warns if missing)
2. Reads constitution for guidelines
3. Researches tech stack and generates `research.md`
4. Creates data model in `data-model.md`
5. Defines API contracts in `contracts/`
6. Generates test scenarios in `quickstart.md`
7. Creates architecture diagrams in `architecture.md`
8. Validates against constitution

**Constitutional Check:** Ensures simplicity, prevents over-engineering.

**Next Step:** `/speckit.tasks`

---

### `/speckit.architecture`

**Purpose:** Generate or update architecture diagrams (usually called by `/plan`).

**Usage:**
```bash
/speckit.architecture
```

**What it does:**
1. Reads spec, data-model, research, plan, quickstart
2. Generates Mermaid diagrams:
   - System Context
   - Component Architecture
   - Data Model (ERD + state machines)
   - Key Flows (3-5 sequence diagrams)
   - Deployment Architecture
   - Technology Stack
3. Writes `architecture.md`

---

### `/speckit.tasks`

**Purpose:** Break implementation plan into ordered, actionable tasks.

**Usage:**
```bash
/speckit.tasks
```

**What it does:**
1. Loads plan, data-model, contracts, research, quickstart
2. Generates task breakdown with:
   - **Setup tasks** - Project init, dependencies
   - **Test tasks [P]** - One per contract/scenario
   - **Core tasks** - One per entity/endpoint
   - **Integration tasks** - Connections, middleware
   - **Polish tasks [P]** - Unit tests, docs
3. Orders by dependencies
4. Marks parallel-safe tasks with `[P]`

**Task Ordering Rules:**
- Same file = sequential
- Different files = can be parallel `[P]`
- Tests before implementation (TDD)

**Next Step:** `/speckit.implement`

---

### `/speckit.implement`

**Purpose:** Execute the implementation by running all tasks in order.

**Usage:**
```bash
/speckit.implement
```

**What it does:**
1. Validates prerequisites (constitution, spec, plan, tasks)
2. Loads all design artifacts
3. Parses task breakdown from `tasks.md`
4. Executes phase-by-phase:
   - Setup → Tests → Core → Integration → Polish
5. Respects dependencies and parallel markers
6. Validates at checkpoints

**Execution Rules:**
- Sequential tasks run in order
- Parallel tasks `[P]` can run together
- Files affecting same location run sequentially
- TDD: Tests before implementation

**Note:** This runs actual CLI commands (npm, dotnet, etc.) - ensure tools are installed.

---

### `/speckit.analyze [target]`

**Purpose:** Analyze existing code, specs, or design artifacts.

**Usage:**
```bash
/speckit.analyze contracts/api-spec.json
/speckit.analyze src/services/
```

**What it does:**
- Performs semantic analysis
- Identifies issues or improvements
- Suggests optimizations
- Validates against best practices

---

### `/speckit.constitution`

**Purpose:** Review or update the project constitution.

**Usage:**
```bash
/speckit.constitution
```

**What it does:**
- Reads `.specify/memory/constitution.md`
- Helps establish project guidelines
- Defines simplicity principles
- Sets coding standards

**Constitution Purpose:** The constitution prevents over-engineering by establishing project-wide principles that all phases must follow.

---

## Permissions Model

Commands can execute bash scripts and fetch web resources. Permissions are defined in `.claude/settings.local.json`:

```json
"permissions": {
  "allow": [
    "Bash(.specify/scripts/bash/create-new-feature.sh:*)",
    "Bash(.specify/scripts/bash/check-prerequisites.sh:*)",
    "Bash(.specify/scripts/bash/setup-plan.sh:*)",
    "WebFetch(domain:github.com)",
    "WebFetch(domain:pkg.go.dev)"
  ],
  "deny": [],
  "ask": []
}
```

### Permission Types

- **`Bash(path:*)`** - Allow specific bash script with any arguments
- **`WebFetch(domain:example.com)`** - Allow fetching from specific domain
- **`deny`** - Explicitly block actions
- **`ask`** - Prompt user for approval each time

### Security Design

✅ **Whitelist approach** - Only explicitly allowed scripts can run
✅ **Path restrictions** - Scripts must be in `.specify/scripts/bash/`
✅ **Domain restrictions** - Web fetches limited to trusted domains
❌ **No arbitrary commands** - Cannot run random bash commands

---

## How This Aligns with Spec-Kit

### Official Spec-Kit Repository

This implementation follows the [official spec-kit design](https://github.com/github/spec-kit) created by GitHub.

### Alignment Points

| Aspect | Official Spec-Kit | This Implementation |
|--------|-------------------|---------------------|
| **Command Namespace** | `/speckit.*` | `/speckit.*` ✅ |
| **Workflow Phases** | Specify → Clarify → Plan → Tasks → Implement | Same ✅ |
| **Directory Structure** | `.specify/` for specs, templates, scripts | Same ✅ |
| **Constitution** | `.specify/memory/constitution.md` | Same ✅ |
| **Templates** | `.specify/templates/*.md` | Same ✅ |
| **Scripts** | Bash scripts for state management | Same ✅ |
| **Artifacts** | Markdown specs in `.specify/specs/` | Same ✅ |
| **Agent Support** | Multi-agent (Claude, Copilot, etc.) | Claude-specific, extensible ✅ |

### Key Differences

1. **Agent Configuration:**
   - Official: Uses various agent-specific files (`.github/copilot-instructions.md`, `CLAUDE.md`, etc.)
   - This: Uses `.claude/settings.local.json` for Cursor/Claude

2. **Command Registration:**
   - Official: May use IDE-level or extension-level registration
   - This: Explicit JSON configuration in workspace

3. **Customization:**
   - Official: General-purpose templates
   - This: Can be customized per-project while maintaining compatibility

### Why This Design?

Spec-kit uses **Spec-Driven Development (SDD)**, which is:

> A systematic approach where you write specifications first, clarify ambiguities, generate detailed plans, and then implement - all guided by AI agents.

**Benefits:**
- ✅ Reduces rework by clarifying requirements early
- ✅ Maintains consistent architecture across features
- ✅ Constitutional principles prevent over-engineering
- ✅ Systematic task breakdown ensures nothing is missed
- ✅ AI-guided but human-controlled workflow

### Spec-Kit Philosophy

From the [spec-kit documentation](https://github.com/github/spec-kit):

1. **Specification First** - Write before coding
2. **Clarification Required** - Resolve ambiguities early
3. **Constitutional Constraints** - Simplicity over complexity
4. **Test-Driven** - Tests before implementation
5. **Incremental Delivery** - User story by user story

This implementation preserves these principles while adapting to Cursor's custom instruction system.

---

## Extending Commands

### Adding a New Command

1. **Create command file:**
```bash
touch .claude/commands/mycommand.md
```

2. **Write command instructions:**
```markdown
---
description: What my command does
---

User input:

$ARGUMENTS

1. Do this first
2. Then do this
3. Finally do this
```

3. **Register in settings:**
```json
{
  "name": "/speckit.mycommand",
  "path": ".claude/commands/mycommand.md"
}
```

4. **Add permissions if needed:**
```json
"Bash(.specify/scripts/bash/my-script.sh:*)"
```

5. **Reload Cursor** to activate

### Modifying Existing Commands

Commands are just markdown files. Edit `.claude/commands/*.md` to change behavior:

```bash
# Edit a command
code .claude/commands/plan.md

# Changes take effect on next execution
# (may need to reload Cursor)
```

### Creating New Templates

Templates guide document generation:

```bash
# Add new template
code .specify/templates/my-template.md

# Reference in commands
# Load `.specify/templates/my-template.md`
```

### Adding Helper Scripts

Scripts manage state and file operations:

```bash
# Create script
code .specify/scripts/bash/my-helper.sh
chmod +x .specify/scripts/bash/my-helper.sh

# Add permission
# "Bash(.specify/scripts/bash/my-helper.sh:*)"

# Call from command
# Run `.specify/scripts/bash/my-helper.sh --json`
```

---

## Troubleshooting

### Commands Not Appearing

- Ensure `.claude/settings.local.json` is valid JSON
- Check that paths in `customInstructions` are correct
- Reload Cursor workspace or restart Cursor

### Permission Errors

- Add required permissions to `allow` array
- Use exact script paths, no wildcards in directory names
- Use `domain:` prefix for web fetches

### Commands Not Working as Expected

- Check command markdown files for syntax errors
- Verify bash scripts have execute permissions
- Ensure prerequisites are met (run `/speckit.clarify` before `/speckit.plan`)

### Arguments Not Passing Through

- Use `$ARGUMENTS` placeholder in command markdown
- Ensure no typos in placeholder name
- Arguments are everything after the command name

---

## Additional Resources

- **Official Spec-Kit:** https://github.com/github/spec-kit
- **Spec-Kit Quick Start:** https://github.github.com/spec-kit/quickstart.html
- **Spec-Driven Development Guide:** Check the spec-kit AGENTS.md

---

## Contributing

To improve these commands:

1. Edit command files in `.claude/commands/`
2. Test thoroughly with various inputs
3. Update this HOWTO if adding new commands
4. Consider contributing improvements back to [spec-kit](https://github.com/github/spec-kit)

---

**Last Updated:** 2025-11-04
**Spec-Kit Version:** Compatible with spec-kit 0.0.79+

