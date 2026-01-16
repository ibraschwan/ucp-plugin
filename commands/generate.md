---
name: generate
description: Generate OpenAPI, OpenRPC, and bundled JSON Schema specs from UCP source schemas
arguments:
  - name: type
    description: "Type of output to generate: all, openapi, openrpc, json-schema, or types"
    required: false
    default: "all"
---

# UCP Generate Command

Generate specification outputs from UCP source schemas.

## What This Command Does

1. Reads source schemas from `source/schemas/shopping/`
2. Resolves all `$ref` references
3. Generates requested output format(s)
4. Writes to `generated/` directory

## Generation Types

| Type | Output | Description |
|------|--------|-------------|
| `all` | All formats | Generate everything (default) |
| `openapi` | `generated/openapi/` | OpenAPI 3.1.0 specs |
| `openrpc` | `generated/openrpc/` | OpenRPC 1.3.2 specs |
| `json-schema` | `generated/json-schema/` | Bundled schemas |
| `types` | TypeScript types | Type definitions |

## Execution

Based on the generation type requested, run the appropriate command:

**For all, openapi, openrpc, json-schema:**
```bash
cd /Users/ibraschwan/bisirepo/ucp && python scripts/generate_schemas.py
```

**For types:**
```bash
cd /Users/ibraschwan/bisirepo/ucp && node scripts/generate_ts_schema_types.js
```

## Post-Generation

After generation completes:
1. Report files created/updated in `generated/`
2. Note any warnings from the generation process
3. Suggest running validation with `/validate`

## Error Handling

If generation fails:
1. Check that all source schemas are valid JSON
2. Verify all `$ref` references resolve
3. Ensure required UCP annotations are present
4. Run `/validate` to identify specific issues
