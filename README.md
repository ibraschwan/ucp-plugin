# UCP Toolkit Plugin

Development toolkit for the [Universal Commerce Protocol (UCP)](https://ucpstore.dev). A Claude Code plugin that provides schema design guidance, validation tools, spec generation commands, and AI-powered agents for building and maintaining UCP-compliant commerce APIs.

**Website:** [ucpstore.dev](https://ucpstore.dev) | **Documentation:** [docs.ucpstore.dev](https://docs.ucpstore.dev)

## Contact & Business Inquiries

**Email: ben@ibracob.dev**

For questions, support, partnerships, or business inquiries, reach out to us directly.

---

## Table of Contents

- [What is UCP?](#what-is-ucp)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Commands](#commands)
- [Agents](#agents)
- [Skills](#skills)
- [Hooks](#hooks)
- [UCP Schema Reference](#ucp-schema-reference)
- [Project Integration](#project-integration)
- [Directory Structure](#directory-structure)
- [Requirements](#requirements)
- [License](#license)

---

## What is UCP?

The **Universal Commerce Protocol** is a standardized specification for e-commerce APIs, built on JSON Schema Draft 2020-12 with custom annotations. UCP defines a unified way for commerce platforms to describe capabilities like carts, checkout, orders, payments, products, customers, and fulfillment.

UCP source schemas are used to generate:

- **OpenAPI 3.1.0** specs for REST APIs
- **OpenRPC 1.3.2** specs for JSON-RPC APIs
- **Bundled JSON Schemas** with all references resolved
- **TypeScript type definitions** for type-safe development

This plugin gives you the tools to design, validate, generate, and document UCP schemas directly from Claude Code.

---

## Installation

```bash
claude plugin install github:ibraschwan/ucp-plugin
```

Or using the slash command inside Claude Code:

```
/plugin install github:ibraschwan/ucp-plugin
```

---

## Quick Start

```bash
# Validate all schemas
/validate

# Generate all specs (OpenAPI, OpenRPC, bundled schemas)
/generate all

# Create TypeScript types
/types generate

# Check for broken $ref references
/check-refs

# Ask the schema designer agent to create a new capability
"Help me create a wishlist capability for UCP"

# Ask the spec validator to debug issues
"My schema changes are failing validation, can you help?"
```

---

## Commands

### `/validate [target]`

Validate UCP schemas, references, and spec compliance.

| Parameter | Default | Options |
|-----------|---------|---------|
| `target` | `all` | `all`, `schemas`, `refs`, `annotations` |

**Validation checks performed:**

- **Schema syntax** — Valid JSON, compliant with JSON Schema Draft 2020-12
- **Required fields** — `$schema` and `$id` present in every schema file
- **Naming conventions** — Definition names use PascalCase
- **Reference resolution** — All `$ref` paths point to existing files, no circular references
- **UCP annotations** — `ucp_request` on request schemas, `ucp_response` on response schemas
- **Method naming** — Methods follow `domain.capability.action` pattern

```bash
/validate              # Full validation suite
/validate schemas      # JSON Schema syntax only
/validate refs         # Reference resolution only
/validate annotations  # UCP annotation compliance only
```

### `/generate [type]`

Generate OpenAPI, OpenRPC, bundled JSON Schema specs, or TypeScript types from source schemas.

| Parameter | Default | Options |
|-----------|---------|---------|
| `type` | `all` | `all`, `openapi`, `openrpc`, `json-schema`, `types` |

**Generation outputs:**

| Type | Format | Output Directory |
|------|--------|------------------|
| `openapi` | OpenAPI 3.1.0 | `generated/openapi/` |
| `openrpc` | OpenRPC 1.3.2 | `generated/openrpc/` |
| `json-schema` | Bundled schemas | `generated/json-schema/` |
| `types` | TypeScript definitions | `types/` |

```bash
/generate              # Generate all formats
/generate openapi      # OpenAPI 3.1.0 only
/generate openrpc      # OpenRPC 1.3.2 only
/generate types        # TypeScript types only
```

The generation pipeline reads source schemas from `source/schemas/shopping/`, resolves all `$ref` references, and writes the output to the corresponding directory.

### `/types [action] [name]`

Generate and explore TypeScript types from UCP schemas.

| Parameter | Default | Options |
|-----------|---------|---------|
| `action` | `generate` | `generate`, `list`, `show` |
| `name` | — | Type name (for `show` action) |

```bash
/types                 # Generate all TypeScript types
/types list            # List available types
/types show money      # Show the money type definition
```

**Available type categories:**

- **Core** — `money.json`, `address.json`, `pagination.json`, `error.json`
- **Product** — `product-variant.json`, `product-option.json`, `inventory.json`
- **Order** — `line-item.json`, `discount.json`, `shipping-rate.json`
- **Customer** — `customer.json`, `payment-method.json`

### `/docs [action] [topic]`

Access and generate UCP documentation.

| Parameter | Default | Options |
|-----------|---------|---------|
| `action` | `view` | `view`, `search`, `generate` |
| `topic` | — | Capability or concept name |

**Capabilities:** `cart`, `checkout`, `order`, `payment`, `product`, `customer`, `fulfillment`

**Concepts:** `annotations`, `methods`, `types`, `architecture`

```bash
/docs view cart        # View cart capability docs
/docs view annotations # View annotation reference
/docs search payment   # Search across all docs
/docs generate         # Generate docs from current schemas
```

### `/check-refs [fix]`

Validate all `$ref` references in UCP schemas, with optional auto-fix.

| Parameter | Default | Options |
|-----------|---------|---------|
| `fix` | `false` | `true`, `false` |

Scans all schemas in `source/schemas/shopping/`, extracts every `$ref` declaration, and verifies each resolves to an existing file or definition.

```bash
/check-refs            # Report broken references
/check-refs true       # Attempt to fix broken references
```

**Valid reference patterns:**

```json
{ "$ref": "types/money.json" }
{ "$ref": "types/address.json#/$defs/PostalAddress" }
{ "$ref": "#/$defs/LocalDefinition" }
```

**Fix mode** identifies likely corrections using Levenshtein distance matching and presents fixes for approval before applying them. Common issues caught: missing `types/` prefix, filename typos, references to non-existent `$defs`, and circular references.

---

## Agents

### Schema Designer (cyan)

An autonomous agent for creating new UCP capability schemas from scratch.

**Trigger phrases:**
- "Help me create a schema for wishlists in UCP"
- "I need to design the gift card method schemas"
- "What's the best way to structure a returns capability?"

**What it does:**

1. Clarifies requirements — operations needed, required fields, relationships to existing types
2. References existing patterns — reads current schemas, identifies reusable types, follows conventions
3. Designs the schema — creates `$defs` with proper annotations, references types from `types/`
4. Validates the design — ensures references resolve, annotations are correct, naming follows conventions

**Output:** Complete, valid JSON Schema files with explanation of design decisions and suggested file location.

### Spec Validator (yellow)

A validation and troubleshooting agent that diagnoses schema issues and suggests fixes.

**Trigger phrases:**
- "My schema changes are failing validation, can you help debug?"
- "Review my new capability schema for any issues"
- "Can you validate my schema changes before I commit?"

**What it does:**

1. Runs automated validation (`python scripts/validate_specs.py`)
2. Checks schema syntax against JSON Schema Draft 2020-12
3. Validates all `$ref` references resolve without circular dependencies
4. Verifies UCP annotations are present and correctly formatted
5. Reviews naming conventions

**Validation report format:**

```
Files Checked: X
Errors: Y
Warnings: Z
─────────────
[Detailed error list with suggested fixes]
[Passed checks confirmation]
```

---

## Skills

Skills provide contextual knowledge that activates automatically when you discuss relevant topics.

### UCP Schema Design

Activates when you mention creating schemas, designing capabilities, adding types, or working with UCP annotations.

**Provides guidance on:**

- JSON Schema Draft 2020-12 structure for UCP
- UCP annotation usage (`ucp_request`, `ucp_response`, `ucp_shared_request`, `ucp_core`)
- Schema file structure and naming conventions
- Type referencing from the 35+ reusable types in `types/`
- Validation rules and best practices

**Bundled references:**

| File | Description |
|------|-------------|
| `references/type-catalog.md` | Complete catalog of 20+ reusable types with field definitions |
| `references/annotation-guide.md` | Full UCP annotation reference with rules and examples |
| `examples/capability-template.json` | Template with CRUD operations, annotations, and type references |

### UCP Protocol Architecture

Activates when you discuss UCP architecture, spec generation, OpenAPI/OpenRPC specs, or method naming.

**Provides guidance on:**

- UCP's layered architecture (types → source schemas → generated specs)
- Method naming convention: `domain.capability.action`
- OpenAPI 3.1.0 generation patterns (REST path mapping, request/response transformation)
- OpenRPC 1.3.2 generation patterns (JSON-RPC method structure, parameter handling)
- Capability design process and core vs capability schema distinctions

**Bundled references:**

| File | Description |
|------|-------------|
| `references/openapi-patterns.md` | OpenAPI generation details — path mapping, security, customization |
| `references/openrpc-patterns.md` | OpenRPC generation details — method structure, error handling, tags |

---

## Hooks

The plugin includes automated hooks that run during schema editing:

### PreToolUse — Schema Validation Guard

Runs before any `Write` or `Edit` operation on schema files. Checks:

- Valid JSON syntax
- Presence of `$schema` and `$id` fields
- UCP annotations on request/response definitions

If validation fails, the write operation is blocked with an explanation of what needs to be fixed.

### PostToolUse — Validation Reminder

Runs after any `Write` or `Edit` operation on UCP schema files. Reminds you to:

- Run `/validate` to check for issues
- Run `/generate` to regenerate specs if needed

---

## UCP Schema Reference

### Annotations

| Annotation | Purpose | Value Format |
|------------|---------|--------------|
| `ucp_request` | Marks a request schema | `"shopping.capability.method"` |
| `ucp_response` | Marks a response schema | `"shopping.capability.method"` |
| `ucp_shared_request` | Marks a schema shared across methods | `["method1", "method2"]` |
| `ucp_core` | Marks protocol-level fields | `true` |

### Method Naming

Methods follow the pattern `domain.capability.action`:

```
shopping.cart.create        # Create a cart
shopping.cart.add_item      # Add item to cart
shopping.checkout.initiate  # Start checkout
shopping.order.get          # Get order details
shopping.product.search     # Search products
```

**Valid actions:** `create`, `get`, `update`, `delete`, `list`, `search`

### Schema File Structure

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://ucp.dev/schemas/shopping/capability-name.json",
  "title": "Capability Name Schemas",
  "$defs": {
    "MethodNameRequest": {
      "type": "object",
      "ucp_request": "shopping.capability.method",
      "properties": { },
      "required": [ ]
    },
    "MethodNameResponse": {
      "type": "object",
      "ucp_response": "shopping.capability.method",
      "properties": { },
      "required": [ ]
    }
  }
}
```

### Core Schemas

| Schema | Capabilities |
|--------|-------------|
| `cart.json` | Cart create, get, update, add/remove items |
| `checkout.json` | Checkout initiation, completion, payment submission |
| `order.json` | Order create, get, list, update, cancel |
| `payment.json` | Payment processing, refunds, method management |
| `product.json` | Product get, list, search, variant management |
| `customer.json` | Customer create, get, update, address management |
| `fulfillment.json` | Fulfillment create, get, tracking, shipment management |

### Reusable Types

Located in `source/schemas/shopping/types/` — 35+ types including:

| Category | Types |
|----------|-------|
| Money & Currency | `money.json` (amount, currency) |
| Address | `address.json` (postal address fields) |
| Product | `product-variant.json`, `product-option.json`, `inventory.json` |
| Order | `line-item.json`, `discount.json`, `shipping-rate.json` |
| Customer | `customer.json`, `payment-method.json` |
| Infrastructure | `pagination.json` (total, page, cursor), `error.json` (code, message, details) |

---

## Project Integration

This plugin is designed to work with UCP spec repositories. The expected project layout:

```
ucp/
├── source/schemas/shopping/        # Source JSON schemas (edit these)
│   ├── cart.json
│   ├── checkout.json
│   ├── order.json
│   ├── payment.json
│   ├── product.json
│   ├── customer.json
│   ├── fulfillment.json
│   └── types/                      # 35+ reusable type definitions
├── generated/                      # Auto-generated (do not edit)
│   ├── openapi/                    # OpenAPI 3.1.0 specs
│   ├── openrpc/                    # OpenRPC 1.3.2 specs
│   └── json-schema/                # Bundled schemas
├── scripts/
│   ├── generate_schemas.py         # Main generation script
│   ├── validate_specs.py           # Validation script
│   └── generate_ts_schema_types.js # TypeScript type generator
└── docs/                           # Documentation
```

### Typical Workflow

1. **Design** — Use the schema designer agent or manually create schemas in `source/schemas/shopping/`
2. **Validate** — Run `/validate` to check syntax, references, and annotations
3. **Fix** — Use `/check-refs true` to auto-fix broken references if any
4. **Generate** — Run `/generate all` to produce OpenAPI, OpenRPC, and bundled schemas
5. **Types** — Run `/types generate` to create TypeScript type definitions
6. **Document** — Run `/docs generate` to create documentation from schemas

---

## Directory Structure

```
ucp-plugin/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest (name, version, metadata)
├── commands/
│   ├── validate.md              # /validate — schema validation
│   ├── generate.md              # /generate — spec generation
│   ├── types.md                 # /types — TypeScript type management
│   ├── docs.md                  # /docs — documentation access
│   └── check-refs.md            # /check-refs — reference checking
├── agents/
│   ├── schema-designer.md       # Autonomous schema creation agent
│   └── spec-validator.md        # Validation and debugging agent
├── skills/
│   ├── ucp-schema/
│   │   ├── SKILL.md             # Schema design guidance
│   │   ├── references/
│   │   │   ├── type-catalog.md  # Complete type catalog
│   │   │   └── annotation-guide.md  # Annotation reference
│   │   └── examples/
│   │       └── capability-template.json  # New capability template
│   └── ucp-protocol/
│       ├── SKILL.md             # Protocol architecture guidance
│       └── references/
│           ├── openapi-patterns.md   # OpenAPI generation patterns
│           └── openrpc-patterns.md   # OpenRPC generation patterns
├── hooks/
│   └── hooks.json               # Pre/post tool use hooks
└── README.md
```

---

## Requirements

- [Claude Code CLI](https://claude.ai/claude-code)
- Python 3.x (for validation and generation scripts)
- Node.js (for TypeScript type generation)

---

## License

MIT

---

**Website:** [ucpstore.dev](https://ucpstore.dev) | **Docs:** [docs.ucpstore.dev](https://docs.ucpstore.dev) | **Contact:** ben@ibracob.dev
