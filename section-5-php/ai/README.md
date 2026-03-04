# Section 5 — PHP 8+ (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 5.1 — Modern PHP Features

```php
<?php

declare(strict_types=1);

// ── Backed enum ───────────────────────────────────────────────────────────────

enum ProductStatus: string
{
    case Active   = 'active';
    case Draft    = 'draft';
    case Archived = 'archived';
}

// ── DTO ───────────────────────────────────────────────────────────────────────

class ProductDTO
{
    // Constructor property promotion with readonly — PHP 8.1+
    public function __construct(
        public readonly string        $name,
        public readonly float         $price,
        public readonly ProductStatus $status,
        public readonly ?string       $sku = null,
    ) {}

    /**
     * Static factory method using named arguments.
     * Rejects unknown keys and collects ALL validation errors (does not fail fast).
     *
     * @param array<string, mixed> $data
     * @throws \InvalidArgumentException with all collected errors joined as one message
     */
    public static function fromArray(array $data): self
    {
        $allowed = ['name', 'price', 'status', 'sku'];
        $errors  = [];

        // Reject unknown keys
        foreach (array_keys($data) as $key) {
            if (!in_array($key, $allowed, strict: true)) {
                $errors[] = "Unknown key: \"{$key}\"";
            }
        }

        // Validate name
        if (!isset($data['name']) || !is_string($data['name']) || trim($data['name']) === '') {
            $errors[] = 'name: must be a non-empty string';
        }

        // Validate price
        if (!isset($data['price']) || !is_numeric($data['price']) || (float) $data['price'] < 0) {
            $errors[] = 'price: must be a non-negative number';
        }

        // Validate status
        $status = null;
        if (!isset($data['status'])) {
            $errors[] = 'status: is required';
        } else {
            $status = ProductStatus::tryFrom((string) $data['status']);
            if ($status === null) {
                $valid    = implode(', ', array_column(ProductStatus::cases(), 'value'));
                $errors[] = "status: must be one of {$valid}";
            }
        }

        if ($errors !== []) {
            throw new \InvalidArgumentException(implode('; ', $errors));
        }

        // Named arguments make the call self-documenting and order-independent
        return new self(
            name:   trim($data['name']),
            price:  (float) $data['price'],
            status: $status,           // non-null guaranteed by validation above
            sku:    isset($data['sku']) ? (string) $data['sku'] : null,
        );
    }

    /**
     * Returns a human-readable label for the current status.
     *
     * Demonstrates: match expression + first-class callable syntax (PHP 8.1+).
     * The labeller closure is lifted into a variable and called via its first-class
     * callable reference — showing that functions are first-class values.
     */
    public function statusLabel(): string
    {
        // First-class callable: the closure is stored as a value, not inlined
        $labeller = static fn(ProductStatus $s): string => match ($s) {
            ProductStatus::Active   => 'Available for sale',
            ProductStatus::Draft    => 'Work in progress',
            ProductStatus::Archived => 'No longer sold',
        };

        return $labeller($this->status);
    }
}

// ── Quick smoke test ──────────────────────────────────────────────────────────

$dto = ProductDTO::fromArray([
    'name'   => 'Blue Widget',
    'price'  => 19.99,
    'status' => 'active',
    'sku'    => 'BW-001',
]);

echo $dto->name . ' — ' . $dto->statusLabel() . PHP_EOL;
// Blue Widget — Available for sale
```

---

## 5.2 — API Design

**JSON error format:**
```json
{
  "code": "VALIDATION_ERROR",
  "message": "The request body contains invalid values.",
  "fields": {
    "price":  ["Must be a positive number."],
    "status": ["Must be one of: active, draft, archived."]
  }
}
```

```php
<?php

declare(strict_types=1);

// ── Helpers ───────────────────────────────────────────────────────────────────

/**
 * Emit a JSON response and terminate.
 *
 * @param array<string, mixed> $body
 */
function jsonResponse(int $status, array $body): never
{
    http_response_code($status);
    header('Content-Type: application/json; charset=UTF-8');
    echo json_encode($body, JSON_THROW_ON_ERROR | JSON_UNESCAPED_UNICODE);
    exit;
}

/**
 * Sanitize a string value to prevent XSS when the value is later embedded
 * in an HTML context. Using ENT_QUOTES | ENT_SUBSTITUTE encodes both single
 * and double quotes and replaces invalid byte sequences.
 */
function sanitizeString(string $value): string
{
    return htmlspecialchars(trim($value), ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
}

// ── Router — PATCH /api/products/{id} ────────────────────────────────────────

$method = $_SERVER['REQUEST_METHOD'] ?? '';
$uri    = parse_url((string) ($_SERVER['REQUEST_URI'] ?? ''), PHP_URL_PATH);

if ($method !== 'PATCH' || !preg_match('#^/api/products/(?P<id>[^/]+)$#', (string) $uri, $m)) {
    jsonResponse(404, ['code' => 'NOT_FOUND', 'message' => 'Endpoint not found.']);
}

$productId = $m['id'];

// ── Parse JSON body ───────────────────────────────────────────────────────────

$rawBody = (string) file_get_contents('php://input');

try {
    /** @var mixed $body */
    $body = json_decode($rawBody, associative: true, flags: JSON_THROW_ON_ERROR);
} catch (\JsonException) {
    jsonResponse(400, ['code' => 'BAD_REQUEST', 'message' => 'Request body must be valid JSON.']);
}

if (!is_array($body)) {
    jsonResponse(400, ['code' => 'BAD_REQUEST', 'message' => 'Request body must be a JSON object.']);
}

if ($body === []) {
    jsonResponse(422, [
        'code'    => 'VALIDATION_ERROR',
        'message' => 'At least one field must be provided.',
    ]);
}

// ── Reject unknown fields ─────────────────────────────────────────────────────

$allowed = ['name', 'price', 'status'];

foreach (array_keys($body) as $key) {
    if (!in_array($key, $allowed, strict: true)) {
        jsonResponse(422, [
            'code'    => 'UNPROCESSABLE_ENTITY',
            'message' => "Unknown field: \"{$key}\".",
        ]);
    }
}

// ── Validate each provided field (collect all errors) ─────────────────────────

/** @var array<string, list<string>> $fieldErrors */
$fieldErrors     = [];
$validatedName   = null;
$validatedPrice  = null;
$validatedStatus = null;

if (array_key_exists('name', $body)) {
    if (!is_string($body['name']) || trim($body['name']) === '') {
        $fieldErrors['name'][] = 'Must be a non-empty string.';
    } else {
        $validatedName = sanitizeString($body['name']);
    }
}

if (array_key_exists('price', $body)) {
    if (!is_numeric($body['price']) || (float) $body['price'] <= 0) {
        $fieldErrors['price'][] = 'Must be a positive number.';
    } else {
        $validatedPrice = (float) $body['price'];
    }
}

if (array_key_exists('status', $body)) {
    $validatedStatus = ProductStatus::tryFrom((string) $body['status']);
    if ($validatedStatus === null) {
        $valid = implode(', ', array_column(ProductStatus::cases(), 'value'));
        $fieldErrors['status'][] = "Must be one of: {$valid}.";
    }
}

if ($fieldErrors !== []) {
    jsonResponse(422, [
        'code'    => 'VALIDATION_ERROR',
        'message' => 'The request body contains invalid values.',
        'fields'  => $fieldErrors,
    ]);
}

// ── Mock DB lookup and update ─────────────────────────────────────────────────

/**
 * @return array<string, mixed>|null
 */
function mockFindProduct(string $id): ?array
{
    // Simulate a DB lookup; return null when not found
    $db = [
        'abc123' => ['id' => 'abc123', 'name' => 'Blue Widget', 'price' => 9.99, 'status' => 'active'],
    ];
    return $db[$id] ?? null;
}

/**
 * @param array<string, mixed> $changes
 * @return array<string, mixed>
 */
function mockUpdateProduct(string $id, array $changes): array
{
    // Simulate a partial update and return the updated record
    $record = mockFindProduct($id) ?? [];
    return array_merge($record, $changes, ['id' => $id]);
}

$existing = mockFindProduct($productId);

if ($existing === null) {
    jsonResponse(404, ['code' => 'NOT_FOUND', 'message' => "Product '{$productId}' not found."]);
}

// Build the partial-update payload from only the provided fields
$changes = array_filter(
    [
        'name'   => $validatedName,
        'price'  => $validatedPrice,
        'status' => $validatedStatus?->value,
    ],
    static fn(mixed $v): bool => $v !== null,
);

$updated = mockUpdateProduct($productId, $changes);

jsonResponse(200, [
    'code'    => 'OK',
    'message' => 'Product updated successfully.',
    'data'    => $updated,
]);
```
