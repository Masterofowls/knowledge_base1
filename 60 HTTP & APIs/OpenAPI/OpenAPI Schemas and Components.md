---
tags: [openapi, concept]
aliases: [components, $ref, oneOf, allOf, JSON Schema]
created: 2026-10-26
status: evergreen
---
# OpenAPI Schemas and Components

```yaml
components:
  schemas:
    ItemCreate:
      type: object
      required: [name, price]
      additionalProperties: false
      properties:
        name: { type: string, minLength: 1, maxLength: 100 }
        price: { type: integer, minimum: 1, description: Price in cents }
        tags: { type: array, items: { type: string }, uniqueItems: true, default: [] }
        color: { type: [string, 'null'], enum: [red, blue, null] }   # 3.1 nullable
    Item:
      allOf:                                  # composition / "inheritance"
        - $ref: '#/components/schemas/ItemCreate'
        - type: object
          required: [id, createdAt]
          properties:
            id: { type: integer, readOnly: true }
            createdAt: { type: string, format: date-time, readOnly: true }

    PaymentMethod:                            # polymorphism
      oneOf:
        - $ref: '#/components/schemas/Card'
        - $ref: '#/components/schemas/BankTransfer'
      discriminator:
        propertyName: type
        mapping:
          card: '#/components/schemas/Card'
          bank: '#/components/schemas/BankTransfer'

    Problem:                                   # RFC 9457
      type: object
      properties:
        type: { type: string, format: uri }
        title: { type: string }
        status: { type: integer }
        detail: { type: string }

  parameters:
    Limit: { name: limit, in: query, schema: { type: integer, minimum: 1, maximum: 100, default: 20 } }

  responses:
    NotFound:
      description: Resource not found
      content: { application/problem+json: { schema: { $ref: '#/components/schemas/Problem' } } }
    ValidationError:
      description: Invalid input
      content: { application/problem+json: { schema: { $ref: '#/components/schemas/Problem' } } }
```

| Keyword | Meaning |
|---|---|
| `allOf` | must match all (merge/extend) |
| `oneOf` | exactly one (tagged unions → [[TS Unions and Narrowing]]) |
| `anyOf` | at least one |
| `readOnly` / `writeOnly` | only in responses / only in requests (e.g. password) |
| `format` | `date-time`, `email`, `uuid`, `uri`, `int64`… |

## Related
- [[FastAPI Pydantic Models]] · [[TS Runtime Validation]] · [[TS Utility Types]] (Create vs Out shapes)
