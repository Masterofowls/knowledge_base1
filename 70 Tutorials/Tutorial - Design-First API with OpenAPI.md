---
tags: [tutorial, openapi, testing]
aliases: [OpenAPI tutorial, contract-first tutorial]
created: 2026-10-26
level: intermediate-advanced
time: 2h
---
# Tutorial - Design-First API with OpenAPI

> [!abstract] What you'll do
> Write an OpenAPI 3.1 spec for the Bookmarks API **before** coding, lint it, serve a mock so a frontend could start immediately, generate TypeScript types, and then contract-test a real implementation against the spec.

**Requires:** Node 22+, Python 3.12+/uv. **Notes used:** [[OpenAPI MOC]], [[OpenAPI Tooling]].

## Step 1 — Write the spec
```yaml
# openapi.yaml
openapi: 3.1.0
info: { title: Bookmarks API, version: 1.0.0 }
servers: [{ url: http://localhost:8000 }]
paths:
  /bookmarks:
    get:
      operationId: listBookmarks
      parameters:
        - { name: tag, in: query, schema: { type: string } }
        - { name: limit, in: query, schema: { type: integer, minimum: 1, maximum: 100, default: 20 } }
      responses:
        '200':
          description: Bookmarks
          content:
            application/json:
              schema: { type: array, items: { $ref: '#/components/schemas/Bookmark' } }
    post:
      operationId: createBookmark
      requestBody:
        required: true
        content: { application/json: { schema: { $ref: '#/components/schemas/BookmarkCreate' } } }
      responses:
        '201':
          description: Created
          content: { application/json: { schema: { $ref: '#/components/schemas/Bookmark' } } }
        '422': { $ref: '#/components/responses/ValidationError' }
  /bookmarks/{bookmarkId}:
    parameters:
      - { name: bookmarkId, in: path, required: true, schema: { type: integer } }
    get:
      operationId: getBookmark
      responses:
        '200': { description: OK, content: { application/json: { schema: { $ref: '#/components/schemas/Bookmark' } } } }
        '404': { $ref: '#/components/responses/NotFound' }
    delete:
      operationId: deleteBookmark
      responses:
        '204': { description: Deleted }
        '404': { $ref: '#/components/responses/NotFound' }
components:
  schemas:
    BookmarkCreate:
      type: object
      required: [url, title]
      properties:
        url: { type: string, format: uri, examples: ['https://example.com'] }
        title: { type: string, minLength: 1, maxLength: 200 }
        tags: { type: array, items: { type: string }, default: [] }
    Bookmark:
      allOf:
        - $ref: '#/components/schemas/BookmarkCreate'
        - type: object
          required: [id, created_at]
          properties:
            id: { type: integer }
            created_at: { type: string, format: date-time }
    Error:
      type: object
      properties: { detail: {} }
  responses:
    NotFound: { description: Not found, content: { application/json: { schema: { $ref: '#/components/schemas/Error' } } } }
    ValidationError: { description: Invalid input, content: { application/json: { schema: { $ref: '#/components/schemas/Error' } } } }
```

## Step 2 — Lint and preview
```bash
npx @redocly/cli lint openapi.yaml
npx @stoplight/spectral-cli lint openapi.yaml --ruleset https://unpkg.com/@stoplight/spectral-rulesets/dist/oas/index.json
npx @redocly/cli preview-docs openapi.yaml       # rendered docs at localhost:8080
```
Fix warnings (missing `description`s, `operationId`s, `info.contact`, etc.).

## Step 3 — Mock server
```bash
npx @stoplight/prism-cli mock openapi.yaml       # → http://127.0.0.1:4010
http :4010/bookmarks
http POST :4010/bookmarks url=https://x.io title=Test       # 201 with an example body
http POST :4010/bookmarks title=                             # 422 — Prism validates requests!
http :4010/bookmarks/1 Prefer:code=404                       # force a specific response
```
> [!info] A frontend team can build against this mock today, while the backend is still being written.

## Step 4 — Generate TypeScript types
```bash
npx openapi-typescript openapi.yaml -o src/api-types.ts
```
```ts
import createClient from "openapi-fetch";
import type { paths } from "./api-types";
const api = createClient<paths>({ baseUrl: "http://127.0.0.1:4010" });
const { data, error } = await api.POST("/bookmarks", { body: { url: "https://x.io", title: "Typed" } });
//            ^? Bookmark                                  ^ compile error if a required field is missing
```

## Step 5 — Contract-test the real implementation
Start the FastAPI service from [[Tutorial - FastAPI CRUD Service with pytest]] on port 8000, then:
```bash
# 1. Proxy mode: Prism validates every real response against YOUR spec
npx @stoplight/prism-cli proxy openapi.yaml http://localhost:8000 --errors --port 4011
http :4011/bookmarks/999        # if the real API's 404 body doesn't match the spec, Prism reports it

# 2. Property-based fuzzing: generates hundreds of requests from the spec
uvx schemathesis run openapi.yaml --url http://localhost:8000 --checks all
```
> [!check] Checkpoint — Schemathesis will probably find something (e.g. huge strings, unexpected 500s, a `created_at` without timezone). Each finding is a real bug or a spec mistake. Fix and re-run until clean.

## Step 6 — Guard against breaking changes
```bash
cp openapi.yaml openapi.v1.yaml
# edit openapi.yaml: rename `title` → `name` in BookmarkCreate
docker run --rm -v "$PWD:/specs" tufin/oasdiff breaking /specs/openapi.v1.yaml /specs/openapi.yaml
```
Add this check to CI so breaking changes need an explicit version bump ([[REST API Design]]).

## 🏋️ Challenges
1. Add `securitySchemes` (bearer JWT) and mark write operations as secured ([[OpenAPI Security Schemes]]).
2. Switch errors to RFC 9457 `application/problem+json`.
3. Compare your hand-written spec with FastAPI's generated `/openapi.json` — what differs? ([[OpenAPI Design-First vs Code-First]])
4. Generate a Python client with `openapi-python-client` and use it in a pytest test.

## Related
- [[Tutorials MOC]] · [[API Testing Strategy]]
