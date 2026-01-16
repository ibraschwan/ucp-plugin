---
name: docs
description: Generate or view UCP documentation for capabilities and types
arguments:
  - name: action
    description: "Action: generate, view, or search"
    required: false
    default: "view"
  - name: topic
    description: "Topic to view or search for"
    required: false
---

# UCP Docs Command

Access and generate UCP documentation.

## What This Command Does

1. For `generate`: Creates documentation from schemas
2. For `view`: Displays documentation for a topic
3. For `search`: Finds relevant documentation

## Actions

### View Documentation

Display documentation for a specific capability or concept:

**Capabilities:**
- `cart` - Shopping cart operations
- `checkout` - Checkout flow
- `order` - Order management
- `payment` - Payment processing
- `product` - Product catalog
- `customer` - Customer data
- `fulfillment` - Order fulfillment

**Concepts:**
- `annotations` - UCP schema annotations
- `methods` - Method naming conventions
- `types` - Reusable type definitions
- `architecture` - Protocol structure

### Search Documentation

Search across UCP documentation:

1. Search in `docs/` directory
2. Search in schema descriptions
3. Search in type definitions

Report matching files and relevant excerpts.

### Generate Documentation

Generate documentation from current schemas:

1. Extract method definitions
2. Build capability reference
3. Create type documentation
4. Output to `docs/generated/`

## Documentation Structure

```
docs/
├── capabilities/
│   ├── cart.md
│   ├── checkout.md
│   └── ...
├── concepts/
│   ├── annotations.md
│   ├── methods.md
│   └── ...
├── types/
│   └── index.md
└── generated/
    └── api-reference.md
```

## Viewing Schemas as Docs

For any capability, extract and present:

1. **Methods** - Available operations
2. **Request schemas** - Input parameters
3. **Response schemas** - Output format
4. **Types used** - Referenced types
5. **Examples** - Usage examples

## Quick Reference

To view a capability's documentation:
1. Read the source schema
2. Extract `ucp_request` annotated definitions
3. Present methods with their request/response schemas
4. Link to referenced types
