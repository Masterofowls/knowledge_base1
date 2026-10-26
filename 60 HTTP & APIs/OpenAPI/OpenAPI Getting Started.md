---
tags: [openapi, guide]
aliases: [Learn OpenAPI, Swagger getting started]
created: 2026-10-26
---
# OpenAPI Getting Started

## 1. Tools to have
- Editor: VS Code + **OpenAPI (Swagger) Editor** / **Redocly** extension, or editor.swagger.io in the browser.
- CLI: `npx @redocly/cli`, `npx @stoplight/prism-cli`, `uvx schemathesis`.

## 2. Smallest valid spec
```yaml
openapi: 3.1.0
info: { title: Hello API, version: 0.1.0 }
paths:
  /hello:
    get:
      operationId: sayHello
      parameters:
        - { name: name, in: query, schema: { type: string, default: world } }
      responses:
        '200':
          description: A greeting
          content:
            application/json:
              schema:
                type: object
                properties: { message: { type: string } }
                required: [message]
```
```bash
npx @redocly/cli lint hello.yaml
npx @stoplight/prism-cli mock hello.yaml
curl "localhost:4010/hello?name=Ada"
```
Or see one generated for you: run any FastAPI app and open `/openapi.json` ([[FastAPI and OpenAPI]]).

## 3. Learning checklist
- [ ] Document structure → [[OpenAPI Document Structure]]
- [ ] Paths, parameters, responses → [[OpenAPI Paths and Operations]]
- [ ] Schemas & `$ref` → [[OpenAPI Schemas and Components]]
- [ ] Security → [[OpenAPI Security Schemes]]
- [ ] Design-first vs code-first → [[OpenAPI Design-First vs Code-First]]
- [ ] Tooling → [[OpenAPI Tooling]]
- [ ] Practise: [[Tutorial - Design-First API with OpenAPI]]

## 4. Beginner mistakes
- Mixing 3.0 and 3.1 syntax (`nullable: true` vs `type: [string, "null"]`).
- Missing `required` arrays → every field optional in generated clients.
- Duplicate / missing `operationId`s → ugly generated method names.
- Spec drifting from implementation (add contract tests).

## Resources
- spec.openapis.org/oas/latest.html
- learn.openapis.org — official guide
- json-schema.org/learn — schemas are JSON Schema
