---
name: types
description: Generate or browse TypeScript types from UCP schemas
arguments:
  - name: action
    description: "Action to perform: generate, list, or show"
    required: false
    default: "generate"
  - name: name
    description: "Type name to show (for show action)"
    required: false
---

# UCP Types Command

Generate and explore TypeScript types from UCP schemas.

## What This Command Does

1. For `generate`: Creates TypeScript type definitions
2. For `list`: Shows available types in `types/` directory
3. For `show`: Displays specific type definition

## Actions

### Generate Types

Generate TypeScript definitions from all schemas:

```bash
cd /Users/ibraschwan/bisirepo/ucp && node scripts/generate_ts_schema_types.js
```

Output location: Determined by script configuration

### List Types

List all available reusable types:

```bash
ls /Users/ibraschwan/bisirepo/ucp/source/schemas/shopping/types/
```

Display as formatted table:
| Type | Description |
|------|-------------|
| money.json | Currency and amount |
| address.json | Postal address |
| ... | ... |

### Show Type

Display a specific type definition. Read the requested type file:

```bash
cat /Users/ibraschwan/bisirepo/ucp/source/schemas/shopping/types/{name}.json
```

Format and explain:
- Properties and their types
- Required fields
- Constraints and validations
- Usage examples in UCP

## Type Categories

**Core Types:**
- `money.json` - Currency amounts
- `address.json` - Postal addresses
- `pagination.json` - List pagination

**Product Types:**
- `product-variant.json` - Product variants
- `product-option.json` - Product options
- `inventory.json` - Stock levels

**Order Types:**
- `line-item.json` - Order line items
- `discount.json` - Discounts applied
- `shipping-rate.json` - Shipping costs

**Customer Types:**
- `customer.json` - Customer data
- `payment-method.json` - Payment methods

## TypeScript Integration

Generated types can be imported:

```typescript
import { Money, Address, LineItem } from './generated/types';
```
