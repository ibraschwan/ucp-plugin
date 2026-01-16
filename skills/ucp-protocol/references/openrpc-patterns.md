# OpenRPC Generation Patterns

How UCP schemas transform into OpenRPC 1.3.2 specifications.

## Overview

The generation script creates OpenRPC specs for JSON-RPC style APIs:

1. Reading all schemas from `source/schemas/shopping/`
2. Extracting `ucp_request` annotated definitions
3. Creating OpenRPC methods
4. Building content descriptors from types

## Method Structure

UCP methods become OpenRPC methods directly:

**UCP Request:**
```json
{
  "CreateCartRequest": {
    "ucp_request": "shopping.cart.create",
    "properties": {
      "customer_id": { "type": "string" }
    }
  }
}
```

**OpenRPC Method:**
```json
{
  "name": "shopping.cart.create",
  "params": [
    {
      "name": "params",
      "schema": { "$ref": "#/components/schemas/CreateCartRequest" }
    }
  ],
  "result": {
    "name": "result",
    "schema": { "$ref": "#/components/schemas/CreateCartResponse" }
  }
}
```

## Generated Structure

```json
{
  "openrpc": "1.3.2",
  "info": {
    "title": "UCP Shopping API",
    "version": "1.0.0"
  },
  "methods": [
    {
      "name": "shopping.cart.create",
      "params": [...],
      "result": {...}
    },
    {
      "name": "shopping.cart.get",
      "params": [...],
      "result": {...}
    }
  ],
  "components": {
    "schemas": {
      "CreateCartRequest": {...},
      "CreateCartResponse": {...},
      "Money": {...}
    }
  }
}
```

## Parameter Handling

All request properties become a single params object:

**UCP Request:**
```json
{
  "properties": {
    "customer_id": { "type": "string" },
    "items": { "type": "array" }
  }
}
```

**OpenRPC Params:**
```json
{
  "params": [
    {
      "name": "params",
      "required": true,
      "schema": {
        "type": "object",
        "properties": {
          "customer_id": { "type": "string" },
          "items": { "type": "array" }
        }
      }
    }
  ]
}
```

## Error Handling

Standard JSON-RPC errors plus UCP-specific:

```json
{
  "errors": [
    {
      "code": -32600,
      "message": "Invalid Request"
    },
    {
      "code": -32601,
      "message": "Method not found"
    },
    {
      "code": 1001,
      "message": "Cart not found",
      "data": { "$ref": "#/components/schemas/Error" }
    }
  ]
}
```

## Type References

Same resolution as OpenAPI:

**Source:**
```json
{ "$ref": "types/money.json" }
```

**OpenRPC:**
```json
{ "$ref": "#/components/schemas/Money" }
```

## Method Metadata

Each method includes:

- `name`: Full method path
- `summary`: From schema description
- `description`: Detailed documentation
- `params`: Request parameters
- `result`: Response schema
- `errors`: Possible errors
- `tags`: Capability grouping

## Tags and Grouping

Methods are tagged by capability:

```json
{
  "name": "shopping.cart.create",
  "tags": [
    { "name": "cart" },
    { "name": "shopping" }
  ]
}
```

## JSON-RPC Usage

Generated spec enables JSON-RPC calls:

```json
{
  "jsonrpc": "2.0",
  "method": "shopping.cart.create",
  "params": {
    "customer_id": "cust_123"
  },
  "id": 1
}
```

Response:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "cart": {
      "id": "cart_456",
      "items": []
    }
  },
  "id": 1
}
```
