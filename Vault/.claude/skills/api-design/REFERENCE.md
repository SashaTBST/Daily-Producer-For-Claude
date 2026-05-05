# API Design Skill — Reference
REST / OpenAPI 3.1 / OWASP API Security Top 10 | Last verified: 2026-04-30

## Research Sources
- REST vs GraphQL vs tRPC vs gRPC 2026: https://dev.to/pockit_tools/rest-vs-graphql-vs-trpc-vs-grpc-in-2026-the-definitive-guide-to-choosing-your-api-layer-1j8m
- OpenAPI 3.1 spec: https://spec.openapis.org/oas/v3.1.0.html
- OWASP API Security Top 10: https://owasp.org/API-Security/
- REST API best practices 2026: https://anakin.ai/blog/best-api-best-practices-2026/
- Scalar vs Redoc vs Swagger UI 2026: https://www.pkgpulse.com/blog/scalar-vs-redoc-vs-swagger-ui-api-documentation-2026

---

## REST URL Conventions

```
✗ NEVER                      ✓ CORRECT
/getUser                     /users/{id}
/createUser                  POST /users
/deleteUser?id=1             DELETE /users/1
/getUserOrders               /users/{id}/orders
/api/user                    /v1/users
/user_orders                 /users/{id}/orders
```

---

## HTTP Status Codes — Quick Reference

| Code | Meaning | When to use |
|------|---------|-------------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST that creates a resource |
| 204 | No Content | Successful DELETE (no body) |
| 400 | Bad Request | Invalid input, validation failure |
| 401 | Unauthorized | Missing or invalid auth token |
| 403 | Forbidden | Valid auth but insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate creation, version conflict |
| 422 | Unprocessable Entity | Valid format but semantic validation failure |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Unexpected server failure |

---

## Consistent Error Format

```json
{
    "error": "User not found",
    "code": "USER_NOT_FOUND",
    "details": {
        "user_id": 42
    }
}
```

Never return `{"success": false}` or `{"status": "error"}` — no code, no detail.
Never return HTTP 200 with an error body.

---

## Cursor Pagination (preferred over offset)

```
GET /v1/posts?limit=20&cursor=eyJpZCI6MTAwfQ

Response:
{
    "data": [...],
    "pagination": {
        "next_cursor": "eyJpZCI6MTIwfQ",
        "has_more": true
    }
}
```

**Why cursor over offset:** Offset breaks when data is inserted/deleted between pages. Cursor is stable. Use cursor for all user-facing infinite scroll or large datasets.

---

## OpenAPI 3.1 Template

```yaml
openapi: 3.1.0
info:
  title: My API
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

security:
  - bearerAuth: []

paths:
  /users:
    post:
      summary: Create a user
      operationId: createUser
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    CreateUserRequest:
      type: object
      required: [email, name]
      properties:
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 2

    UserResponse:
      type: object
      properties:
        id:
          type: integer
        email:
          type: string
        name:
          type: string

  responses:
    BadRequest:
      description: Invalid input
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
    Unauthorized:
      description: Missing or invalid auth
```

---

## OWASP API Security Top 10 (2023 — current in 2026)

| # | Risk | What it means | Fix |
|---|------|--------------|-----|
| API1 | BOLA | Accessing other users' objects (`/users/2` when logged in as user 1) | Check object ownership on every request |
| API2 | Broken Auth | Weak token validation, no expiry | JWT with short expiry, refresh tokens |
| API3 | Broken Object Property Auth | Returning admin fields to regular users | Field-level access control, response filtering |
| API4 | Unrestricted Resource Consumption | No rate limiting, no request size limits | Rate limiting + request size caps |
| API5 | Broken Function Auth | Regular user calling admin endpoints | Role-based middleware on every route |
| API6 | Unrestricted Access to Sensitive Flows | Password reset called unlimited times | Rate limit sensitive operations |
| API7 | SSRF | Server-side URL fetch with user-supplied URL → internal metadata access | Allowlist + block internal IPs |
| API8 | Security Misconfiguration | Default creds, verbose errors, CORS * | Hardened config, generic error messages |
| API9 | Improper Inventory Management | Old API versions still running | Version deprecation policy, sunset headers |
| API10 | Unsafe API Consumption | Trusting external API responses without validation | Validate and sanitize all external data |

---

## DESIGN REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | Nouns in URLs | Verbs in URL paths (`/getUser`, `/createOrder`) | PASS/FAIL |
| 2 | Versioning | No `/v1/` prefix or `Accept: application/vnd.v1+json` header | PASS/FAIL |
| 3 | Correct status codes | 200 returned for POST creates, 200 returned for errors | PASS/FAIL |
| 4 | Rate limiting | No rate limit headers or documented limits on public endpoints | PASS/FAIL |
| 5 | Auth documented | Any endpoint missing auth scheme documentation | PASS/FAIL |
| 6 | Pagination | List endpoints returning all records without pagination | PASS/WARN |
| 7 | Consistent error format | Error responses without `error`, `code`, `details` structure | PASS/WARN |
| 8 | BOLA check | Object endpoints without ownership verification pattern | PASS/FAIL |
| 9 | Field exposure | Response schemas returning internal fields (passwords, tokens) | PASS/FAIL |
| 10 | Request size limit | No documented request body size limit | PASS/WARN |
