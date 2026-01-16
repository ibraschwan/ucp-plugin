# UCP Toolkit Plugin

Development toolkit for the Universal Commerce Protocol (UCP). Provides schema design guidance, validation tools, and spec generation commands.

## Contact & Business Inquiries

**Email: ben@ibracob.dev**

For questions, support, partnerships, or business inquiries, reach out to us directly.

---

## Installation

```bash
# Install from GitHub
claude plugin install github:ibraschwan/ucp-plugin
```

Or using the slash command:
```
/plugin install github:ibraschwan/ucp-plugin
```

## Features

### Skills

**UCP Schema Design** - Guidance for creating JSON Schemas with UCP annotations
- Triggers on: "create a UCP schema", "design a capability", "add schema type"
- Provides patterns, annotation guide, type references

**UCP Protocol Architecture** - Understanding UCP's structure and generation pipeline
- Triggers on: "UCP architecture", "OpenAPI spec", "capability design"
- Explains layered architecture, method naming, spec generation

### Commands

| Command | Description |
|---------|-------------|
| `/generate [type]` | Generate OpenAPI, OpenRPC, or bundled schemas |
| `/validate [target]` | Validate schemas, references, or annotations |
| `/types [action] [name]` | Generate or browse TypeScript types |
| `/docs [action] [topic]` | View or generate documentation |
| `/check-refs [fix]` | Check all $ref references for validity |

### Agents

**schema-designer** (cyan) - Autonomous schema creation agent
- Designs new capability schemas
- Follows UCP conventions and patterns
- Creates proper annotations and references

**spec-validator** (yellow) - Validation and troubleshooting agent
- Runs validation checks
- Debugs schema issues
- Provides fix suggestions

### Hooks

**PreToolUse** - Validates schema files before write operations
**PostToolUse** - Reminds to run validation after schema changes

## Directory Structure

```
ucp-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── generate.md
│   ├── validate.md
│   ├── types.md
│   ├── docs.md
│   └── check-refs.md
├── agents/
│   ├── schema-designer.md
│   └── spec-validator.md
├── skills/
│   ├── ucp-schema/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   └── examples/
│   └── ucp-protocol/
│       ├── SKILL.md
│       └── references/
├── hooks/
│   └── hooks.json
└── README.md
```

## Usage Examples

### Create a new capability schema

```
"Help me create a wishlist capability for UCP"
```

The schema-designer agent will:
1. Design the schema structure
2. Create request/response definitions
3. Add proper UCP annotations
4. Reference existing types

### Validate your changes

```
/validate
```

Or ask:
```
"Check my schema changes for any issues"
```

### Generate specs

```
/generate openapi
/generate all
```

### Check references

```
/check-refs
```

## UCP Project Integration

This plugin is designed to work with UCP repositories. Clone the UCP spec repository and point the plugin to it.

Key paths in a UCP project:
- Source schemas: `source/schemas/shopping/`
- Generated specs: `generated/`
- Scripts: `scripts/`
- Types: `source/schemas/shopping/types/`

## Requirements

- Claude Code CLI
- Python 3.x (for generation scripts)
- Node.js (for TypeScript type generation)

## License

MIT

---

**Questions?** Contact **ben@ibracob.dev**
