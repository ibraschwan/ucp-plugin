---
name: validate
description: Validate UCP schemas, references, and spec compliance
arguments:
  - name: target
    description: "What to validate: all, schemas, refs, or annotations"
    required: false
    default: "all"
---

# UCP Validate Command

Validate UCP source schemas for correctness and compliance.

## What This Command Does

1. Loads all source schemas from `source/schemas/shopping/`
2. Performs requested validation checks
3. Reports any issues found
4. Provides fix suggestions

## Validation Types

| Type | Checks | Description |
|------|--------|-------------|
| `all` | Everything | Full validation suite (default) |
| `schemas` | JSON Schema syntax | Valid JSON Schema Draft 2020-12 |
| `refs` | Reference resolution | All `$ref` paths resolve |
| `annotations` | UCP annotations | Required annotations present |

## Execution

Run the validation script:

```bash
cd /Users/ibraschwan/bisirepo/ucp && python scripts/validate_specs.py
```

## Validation Checks

### Schema Validation
- Valid JSON syntax
- Complies with JSON Schema Draft 2020-12
- Required `$schema` and `$id` fields present
- Definition names follow PascalCase convention

### Reference Validation
- All `$ref` paths point to existing files
- No circular references that break resolution
- Type references in `types/` directory exist

### Annotation Validation
- Request schemas have `ucp_request` annotation
- Response schemas have `ucp_response` annotation
- Method names follow `domain.capability.action` pattern
- Shared requests use `ucp_shared_request` correctly

## Output Format

Report validation results:
- Total schemas checked
- Errors found (must fix)
- Warnings (should review)
- Passed checks

## Fix Suggestions

For each issue found, provide:
1. File path and line number
2. Issue description
3. Suggested fix
4. Example of correct format
