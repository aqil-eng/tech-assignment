# Section 5 — PHP 8+
## 5.1 — Modern PHP Features

Write a PHP 8.2+ class called `ProductDTO` that demonstrates:

- Constructor property promotion with `readonly` properties
- A backed enum called `ProductStatus` (`active`, `draft`, `archived`) used as a property type
- A named arguments static factory method `fromArray(array $data): self`
- Safe construction: reject unknown keys and collect validation errors (do not fail fast)
- A method using either:
  - `match` expressions AND first-class callable syntax
  **OR**
  - `match` expressions AND fibers

```php
// Write ProductStatus + ProductDTO here
```

---

## 5.2 — API Design

You are building a JSON REST API endpoint in PHP (no framework, or a micro-framework of your choice).

Write the handler for:

`PATCH /api/products/{id}`

It must:

1. Validate incoming JSON body, accepting only:
   - `name`
   - `price`
   - `status`
2. Use the `ProductStatus` enum from 5.1 for status validation
3. Return appropriate HTTP status codes (`200`, `400`, `404`, `422`) with JSON error responses
4. Implement input sanitization against XSS
5. Use type-safe return types and union types where appropriate

**Additional Requirement**
- Show the exact JSON error format you return (must include `code`, `message`, and optional `fields`).

**JSON error format:**
```json
// Show your error format here
```

```php
// Write endpoint handler here
```
