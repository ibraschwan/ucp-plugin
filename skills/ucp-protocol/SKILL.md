---
name: UCP Protocol Architecture
description: This skill should be used when the user asks about "UCP architecture", "protocol structure", "OpenAPI spec", "OpenRPC spec", "capability design", "method naming", or needs to understand how UCP works. Provides guidance on UCP's layered architecture and spec generation.
version: 1.0.0
---

# UCP Protocol Architecture

## Overview

Universal Commerce Protocol (UCP) is an open standard for commerce interoperability. It enables AI agents and applications to interact with e-commerce systems through a unified API specification.

## Protocol Layers

```
┌─────────────────────────────────────────┐
│           Generated Specs               │
│  (OpenAPI 3.1.0 + OpenRPC 1.3.2)       │
├─────────────────────────────────────────┤
│           Source Schemas                │
│      (JSON Schema Draft 2020-12)        │
├─────────────────────────────────────────┤
│          Type Definitions               │
│      (Reusable Commerce Types)          │
└─────────────────────────────────────────┘
```

## Directory Structure

```
ucp/
├── source/
│   └── schemas/
│       └── shopping/           # Source JSON schemas
│           ├── checkout.json
│           ├── order.json
│           ├── payment.json
│           ├── product.json
│           ├── cart.json
│           ├── customer.json
│           ├── fulfillment.json
│           └── types/          # Reusable types
├── generated/
│   ├── openapi/               # OpenAPI 3.1.0 specs
│   │   └── shopping.openapi.json
│   ├── openrpc/               # OpenRPC 1.3.2 specs
│   │   └── shopping.openrpc.json
│   └── json-schema/           # Bundled schemas
│       └── shopping.schema.json
├── scripts/
│   ├── generate_schemas.py    # Main generation script
│   ├── validate_specs.py      # Validation script
│   └── generate_ts_schema_types.js
└── docs/                      # Documentation
```

## Method Naming Convention

UCP methods follow dot notation: `domain.capability.action`

**Examples:**
- `shopping.cart.create` - Create a cart
- `shopping.cart.add_item` - Add item to cart
- `shopping.checkout.initiate` - Start checkout
- `shopping.order.get` - Get order details
- `shopping.product.search` - Search products

## Generation Pipeline

Source schemas are processed to generate:

1. **OpenAPI 3.1.0** - REST API specification
2. **OpenRPC 1.3.2** - JSON-RPC specification
3. **Bundled JSON Schema** - Dereferenced schemas
4. **TypeScript Types** - Type definitions

### Running Generation

```bash
# Generate all specs
python scripts/generate_schemas.py

# Generate TypeScript types
node scripts/generate_ts_schema_types.js
```

## Validation Pipeline

Validation ensures schema correctness:

```bash
# Validate all specs
python scripts/validate_specs.py
```

**Validation checks:**
- JSON Schema syntax valid
- All `$ref` references resolve
- UCP annotations present
- Method names follow convention
- Required fields defined

## Capability Design Process

To add a new capability:

1. **Design types** - Create reusable types in `types/`
2. **Define schemas** - Create capability schema with request/response
3. **Add annotations** - Apply `ucp_request`/`ucp_response`
4. **Validate** - Run `validate_specs.py`
5. **Generate** - Run `generate_schemas.py`
6. **Review** - Check generated OpenAPI/OpenRPC

## Core vs Capability Schemas

**Core schemas** (`ucp_core: true`):
- Protocol-level fields
- Error handling
- Pagination
- Common metadata

**Capability schemas**:
- Domain-specific operations
- Business logic
- Commerce entities

## Additional Resources

### Reference Files

For detailed architecture documentation:
- **`references/openapi-patterns.md`** - OpenAPI generation patterns
- **`references/openrpc-patterns.md`** - OpenRPC generation patterns

### Examples

- **`examples/full-capability-flow.md`** - End-to-end capability creation
- **`examples/method-naming.md`** - Method naming examples
