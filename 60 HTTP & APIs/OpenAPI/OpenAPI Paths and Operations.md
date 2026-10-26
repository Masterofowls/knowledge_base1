---
tags: [openapi, snippet]
aliases: [openapi paths, operationId, requestBody, responses]
created: 2026-10-26
status: evergreen
---
# OpenAPI Paths and Operations

```yaml
paths:
  /items:
    get:
      tags: [items]
      operationId: listItems
      summary: List items
      parameters:
        - name: q
          in: query
          schema: { type: string, maxLength: 50 }
        - $ref: '#/components/parameters/Limit'
      responses:
        '200':
          description: A page of items
          headers:
            X-Total-Count: { schema: { type: integer } }
          content:
            application/json:
              schema:
                type: array
                items: { $ref: '#/components/schemas/Item' }
    post:
      tags: [items]
      operationId: createItem
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/ItemCreate' }
            examples:
              pen: { value: { name: Pen, price: 150 } }
      responses:
        '201':
          description: Created
          headers:
            Location: { schema: { type: string, format: uri } }
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Item' }
        '422': { $ref: '#/components/responses/ValidationError' }

  /items/{itemId}:
    parameters:
      - name: itemId
        in: path
        required: true          # path params are always required
        schema: { type: integer, minimum: 1 }
    get:
      operationId: getItem
      responses:
        '200': { description: OK, content: { application/json: { schema: { $ref: '#/components/schemas/Item' } } } }
        '404': { $ref: '#/components/responses/NotFound' }
    delete:
      operationId: deleteItem
      security: [{ bearerAuth: [] }]
      responses:
        '204': { description: Deleted }
```

Parameter locations: `path`, `query`, `header`, `cookie`.

> [!tip] Unique, verb-first `operationId`s become method names in generated clients (`client.createItem()`).

## Related
- [[HTTP Methods]] · [[HTTP Status Codes]] · [[FastAPI Routing and Parameters]]
