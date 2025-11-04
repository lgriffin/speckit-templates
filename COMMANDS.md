# Spec-Kit Commands - Customization Guide

This document explains how to use and customize spec-kit commands with AI coding agents.

## Overview

This repository demonstrates **spec-kit customization** - how to add local command shortcuts that work alongside the global spec-kit commands.

### Two-Layer Command System

**1. Global Commands** (built into spec-kit):
- `/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, `/speckit.implement`
- Provided automatically by spec-kit
- Use default implementations
- Work across all projects

**2. Local Shortcuts** (this customization):
- `/specify`, `/plan`, `/tasks`, `/implement`
- Defined in `.claude/settings.local.json`
- Use customized implementations in `.claude/commands/*.md`
- Can be tailored to your workflow

## Available Commands

| Local Shortcut | Global Command | Purpose | Difference |
|----------------|----------------|---------|------------|
| `/specify` | `/speckit.specify` | Create feature specification | Same |
| `/clarify` | `/speckit.clarify` | Ask clarifying questions | Same |
| `/plan` | `/speckit.plan` | Generate implementation plan | **Custom includes architecture** |
| `/architecture` | N/A | Generate architecture diagrams | **Custom only** |
| `/tasks` | `/speckit.tasks` | Break plan into tasks | Same |
| `/analyze` | `/speckit.analyze` | Analyze existing code/specs | Same |
| `/implement` | `/speckit.implement` | Execute implementation | Same |
| `/constitution` | `/speckit.constitution` | Review project constitution | Same |

**Key Differences:**
- `/plan` (custom) automatically generates `architecture.md` with Mermaid diagrams
- `/speckit.plan` (global) does NOT generate architecture - you'd need to run a separate command
- `/architecture` is a custom command added in this template, not part of core spec-kit

## How Customization Works

### Default Spec-Kit Init

When you run `specify init my-project --ai claude`, you get:

```
my-project/
├── .claude/
│   └── commands/
│       ├── speckit.specify.md
│       ├── speckit.plan.md
│       └── ... (other global commands)
└── .specify/
    ├── memory/constitution.md
    ├── scripts/...
    └── templates/...
```

This gives you the **global `/speckit.*` commands**.

### This Template's Customization

This repository **extends** the default init by adding:

```
.claude/
├── settings.local.json          # ← Registers local shortcuts
└── commands/
    ├── speckit.*.md            # Global defaults (from init)
    ├── specify.md              # ← Custom /specify
    ├── plan.md                 # ← Custom /plan
    ├── architecture.md          # ← Custom /architecture
    └── ... (other customs)
```

Now you have **both** options:
- Use `/speckit.plan` → runs default spec-kit behavior
- Use `/plan` → runs your customized version

### Why This Approach?

✅ **Backwards compatible** - Global commands still work  
✅ **Flexible** - Override only what you want  
✅ **Shareable** - Commit `.claude/` to share team customizations  
✅ **Clear intent** - Local shortcuts show you've customized the workflow

## Using This Template

### Option 1: Apply to New Project

```bash
# Initialize with spec-kit
specify init my-new-project --ai claude

# Copy customizations from this template
cp -r speckit-templates/.claude my-new-project/
cp -r speckit-templates/.specify/* my-new-project/.specify/

# Or, if you want to merge:
# Review and manually merge .specify/ contents
```

### Option 2: Apply to Existing Project

```bash
cd my-existing-project

# Copy custom command shortcuts
cp -r ../speckit-templates/.claude .

# Selectively merge .specify/ customizations
# (Review each file to avoid overwriting your constitution, etc.)
```

### Option 3: Fork This Template

1. Fork/clone this repository
2. Customize `.claude/commands/*.md` to your workflow
3. Customize `.specify/templates/*.md` to your standards
4. Use as your project starter template

## Customization Examples

### Customize the /plan Command

The custom `/plan` command in this template already includes automatic architecture generation:

Edit `.claude/commands/plan.md` to add more customizations:

```markdown
---
description: My custom planning workflow with architecture
---

## Outline

1. Run setup script...
2. Generate research, data-model, contracts...
3. Update agent context...
4. **Generate architecture documentation** ← Already added!
5. Add your own custom steps...
```

**Current customization**: `/plan` automatically generates `architecture.md` with diagrams, while `/speckit.plan` does not.

You can add more customizations like:
- Additional validation steps
- Custom artifact generation
- Team-specific documentation
- etc.

### Add New Custom Command

1. Create `.claude/commands/mycommand.md`
2. Add to `.claude/settings.local.json`:

```json
{
  "customInstructions": [
    {
      "name": "/mycommand",
      "path": ".claude/commands/mycommand.md"
    }
  ]
}
```

3. Restart Cursor

### Customize Templates

Edit files in `.specify/templates/`:
- `spec-template.md` - Feature specification structure
- `plan-template.md` - Implementation plan format
- `tasks-template.md` - Task breakdown format

Your custom `/plan` command will use these templates.

## Typical Workflow

### Phase 1: Specification
```bash
/specify Create a real-time chat app with rooms and presence
# or
/speckit.specify Create a real-time chat app...

/clarify
# AI asks targeted questions

# Review: .specify/specs/###-feature-name/spec.md
```

### Phase 2: Planning
```bash
/plan Use Node.js, Socket.IO, React, PostgreSQL
# Custom workflow - automatically includes architecture generation
# Generates:
# - plan.md
# - data-model.md
# - research.md
# - contracts/
# - quickstart.md
# - architecture.md (with Mermaid diagrams) ← Automatic!

# OR use global command (no architecture):
/speckit.plan Use Node.js...
# Then manually run:
# /architecture
# (if you want architecture diagrams)
```

### Phase 3: Tasks & Implementation
```bash
/tasks
# Generates tasks.md

/implement
# Executes implementation
```

## Design Philosophy

### Spec-Kit's Approach

Spec-kit uses **global commands** to ensure:
- Consistency across projects
- No per-project configuration needed
- Multi-agent support (Claude, Copilot, etc.)
- Centralized updates

### This Template's Approach

Add **local shortcuts** to enable:
- Team-specific workflows
- Company coding standards
- Custom validation steps
- Additional artifact generation
- Workflow automation

**Key principle**: Customize via addition, not replacement. Global commands remain available.

## File Structure

```
your-project/
├── .claude/
│   ├── settings.local.json      # Command registrations
│   ├── README.md                # This guidance
│   └── commands/
│       ├── speckit.*.md        # Global defaults (from init)
│       └── *.md                # Custom shortcuts
├── .specify/
│   ├── memory/
│   │   └── constitution.md
│   ├── scripts/
│   │   ├── bash/...
│   │   └── powershell/...
│   ├── specs/
│   │   └── ###-feature-name/
│   │       ├── spec.md
│   │       ├── plan.md
│   │       ├── architecture.md
│   │       └── ...
│   └── templates/
│       ├── spec-template.md
│       ├── plan-template.md
│       └── ...
└── [your project files]
```

## Troubleshooting

### Commands Not Appearing

1. **Check file exists**: `.claude/settings.local.json`
2. **Verify JSON syntax**: Use a validator
3. **Restart Cursor**: Completely quit and reopen
4. **Check paths**: Command paths in settings must match actual files

### Both Global and Local Commands Visible

**This is correct!** You should see:
- ✅ `/specify` AND `/speckit.specify`
- ✅ `/plan` AND `/speckit.plan`
- etc.

Use whichever you prefer. They serve different purposes.

### Want Only Local Commands

You can't hide global commands, but you can:
- Consistently use local shortcuts (`/plan` not `/speckit.plan`)
- Document your team's convention
- Train team to use shortcuts

### Commands Execute Wrong Behavior

Check which command you're using:
- `/plan` → Uses `.claude/commands/plan.md` (your custom)
- `/speckit.plan` → Uses `.claude/commands/speckit.plan.md` (default)

## Additional Resources

- **Official Spec-Kit**: https://github.com/github/spec-kit
- **Spec-Kit Documentation**: Check AGENTS.md in spec-kit repo
- **This Template**: See `.claude/README.md` for more details

## Contributing

To improve this template:

1. Customize `.claude/commands/*.md` files
2. Update `.specify/templates/*.md` as needed
3. Test with actual projects
4. Document changes in this file
5. Share improvements!

---

**Last Updated:** 2025-11-04  
**Spec-Kit Compatibility:** 0.0.79+  
**Approach:** Additive customization (extends, not replaces)
