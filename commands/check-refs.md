---
name: check-refs
description: Check all $ref references in UCP schemas for validity
arguments:
  - name: fix
    description: "Attempt to fix broken references: true or false"
    required: false
    default: "false"
---

# UCP Check References Command

Validate all `$ref` references in UCP schemas.

## What This Command Does

1. Scans all schemas in `source/schemas/shopping/`
2. Extracts all `$ref` declarations
3. Verifies each reference resolves to an existing file
4. Reports broken or suspicious references

## Execution

Search for all `$ref` in schema files:

```bash
grep -r '"\$ref"' /Users/ibraschwan/bisirepo/ucp/source/schemas/shopping/ --include="*.json"
```

For each reference found:
1. Extract the reference path
2. Resolve relative to the containing file
3. Check if target file exists
4. Verify target contains referenced definition

## Reference Patterns

**Valid patterns:**
```json
{ "$ref": "types/money.json" }
{ "$ref": "types/address.json#/$defs/PostalAddress" }
{ "$ref": "#/$defs/LocalDefinition" }
```

**Common issues:**
- Missing `types/` prefix
- Typos in type names
- Referencing non-existent `$defs`
- Circular references

## Output Format

Report reference status:

```
Checking references in source/schemas/shopping/...

✓ cart.json
  - types/money.json ✓
  - types/line-item.json ✓
  - #/$defs/CartItem ✓

✗ checkout.json
  - types/monye.json ✗ (not found - did you mean types/money.json?)
  - types/shipping.json ✓

Summary:
- Files checked: 7
- References found: 45
- Valid: 44
- Broken: 1
```

## Fix Mode

When `fix=true`:

1. Identify likely correct reference for broken refs
2. Suggest corrections based on:
   - Similar file names (Levenshtein distance)
   - Common typos
   - Missing prefixes
3. Present fixes for approval before applying

## Prevention

To prevent broken references:
1. Always use `/check-refs` before committing
2. Run as part of CI/CD pipeline
3. Use IDE with JSON Schema support
4. Reference types by exact filename
