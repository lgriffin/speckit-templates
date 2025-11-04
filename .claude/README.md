# Claude Command Configuration

This directory contains command configuration for spec-kit with Cursor/Claude integration.

## Structure

```
.claude/
├── settings.local.json          # Local command shortcut registrations
└── commands/
    ├── speckit.*.md            # Global spec-kit command implementations
    └── *.md                    # Custom local shortcuts
```

## How It Works

### Two-Layer Command System

**Global Commands** (provided by spec-kit):
- `/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, etc.
- Use files: `commands/speckit.*.md`
- Available in all spec-kit projects

**Local Shortcuts** (this customization):
- `/specify`, `/plan`, `/tasks`, etc.
- Use files: `commands/*.md` (without prefix)
- Registered in: `settings.local.json`
- Can be customized per-project

### Why Both?

You can use either command style:
- **Global** (`/speckit.plan`) - Standard spec-kit behavior
- **Local** (`/plan`) - Your customized workflow

Benefits:
- Customize workflows while keeping spec-kit defaults available
- Override specific commands without affecting others
- Share customizations across team by committing this directory

## Customization Approach

1. **Default init** creates `commands/speckit.*.md` files
2. **Your template** adds:
   - `settings.local.json` - Registers shortcuts
   - `commands/*.md` - Custom implementations
   - Modified `.specify/` templates/scripts

This allows your customizations to override the default init while maintaining compatibility.

## Command Registration

`settings.local.json` registers local shortcuts:

```json
{
  "customInstructions": [
    {
      "name": "/plan",
      "path": ".claude/commands/plan.md"
    }
  ]
}
```

**Important**: Use non-namespaced names (`/plan` not `/speckit.plan`).

## Permissions

Commands can:
- ✅ Run specific bash/PowerShell scripts in `.specify/scripts/`
- ✅ Fetch from approved domains (github.com, pkg.go.dev, etc.)
- ❌ NOT run arbitrary commands
- ❌ NOT access unapproved domains

## Modifying Commands

1. Edit `commands/*.md` files to customize behavior
2. Changes take effect on next command execution
3. Keep `commands/speckit.*.md` for reference

## Security Note

Consider adding `.claude/` to `.gitignore` if it will contain:
- Auth tokens
- API keys
- Other sensitive data

For this template repository, `.claude/` should be committed to share customizations.

---

**Compatible with**: spec-kit 0.0.79+

