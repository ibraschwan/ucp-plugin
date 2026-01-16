# OpenAPI Generation Patterns

How UCP schemas transform into OpenAPI 3.1.0 specifications.

## Overview

The generation script (`scripts/generate_schemas.py`) transforms source schemas into OpenAPI specs by:

1. Reading all schemas from `source/schemas/shopping/`
2. Extracting `ucp_request` annotated definitions
3. Creating OpenAPI paths and operations
4. Building component schemas from types

## Method to Path Mapping

UCP methods map to REST paths:

| UCP Method | HTTP Method | Path |
|------------|-------------|------|
| shopping.cart.create | POST | /shopping/cart |
| shopping.cart.get | GET | /shopping/cart/{id} |
| shopping.cart.update | PATCH | /shopping/cart/{id} |
| shopping.cart.delete | DELETE | /shopping/cart/{id} |
| shopping.cart.list | GET | /shopping/cart |
| shopping.product.search | POST | /shopping/product/search |

## Request Schema Transformation

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

**OpenAPI Operation:**
```yaml
/shopping/cart:
  post:
    operationId: shopping.cart.create
    requestBody:
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/CreateCartRequest'
```

## Response Schema Transformation

**UCP Response:**
```json
{
  "CreateCartResponse": {
    "ucp_response": "shopping.cart.create",
    "properties": {
      "cart": { "$ref": "types/cart.json" }
    }
  }
}
```

**OpenAPI Response:**
```yaml
responses:
  '200':
    description: Successful response
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/CreateCartResponse'
```

## Type Reference Resolution

References are resolved and inlined:

**Source:**
```json
{ "$ref": "types/money.json" }
```

**OpenAPI:**
```yaml
$ref: '#/components/schemas/Money'
```

Types are added to `components/schemas/`.

## Generated Structure

```yaml
openapi: 3.1.0
info:
  title: UCP Shopping API
  version: 1.0.0
paths:
  /shopping/cart:
    post: ...
    get: ...
  /shopping/cart/{id}:
    get: ...
    patch: ...
    delete: ...
components:
  schemas:
    CreateCartRequest: ...
    CreateCartResponse: ...
    Money: ...
    Address: ...
```

## Path Parameter Extraction

ID fields become path parameters:

**Request with ID:**
```json
{
  "GetCartRequest": {
    "properties": {
      "id": { "type": "string" }
    },
    "required": ["id"]
  }
}
```

**OpenAPI Path:**
```yaml
/shopping/cart/{id}:
  get:
    parameters:
      - name: id
        in: path
        required: true
        schema:
          type: string
```

## Security Definitions

OpenAPI includes security schemes:

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
    apiKey:
      type: apiKey
      in: header
      name: X-API-Key
```

## Customization

The generation script supports:

- Custom path prefixes
- Security scheme selection
- Response code customization
- Tag generation from capabilities
