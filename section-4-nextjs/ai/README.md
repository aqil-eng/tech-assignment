# Section 4 — Next.js App Router (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 4.1 — Data Fetching and Caching

```txt
app/
  products/
    page.tsx       ← Server Component, fetches + renders products (ISR 60 s)
    loading.tsx    ← Shown automatically by Next.js while page.tsx is streaming
    error.tsx      ← Error boundary for this route segment (must be a Client Component)
```

```tsx
// app/products/page.tsx
type Product = {
  id: number;
  name: string;
  price: number;
};

async function getProducts(): Promise<Product[]> {
  const res = await fetch('https://api.example.com/products', {
    // ISR: cache the response and revalidate it at most every 60 seconds.
    // Equivalent to `export const revalidate = 60` at route-segment level.
    next: { revalidate: 60 },
  });

  if (!res.ok) {
    // Throwing here causes Next.js to render the nearest error.tsx boundary.
    throw new Error(`Failed to fetch products (${res.status})`);
  }

  return res.json() as Promise<Product[]>;
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>
      <ul>
        {products.map(product => (
          <li key={product.id}>
            {product.name} — ${product.price.toFixed(2)}
          </li>
        ))}
      </ul>
    </main>
  );
}
```

```tsx
// app/products/loading.tsx
// Next.js renders this file automatically while ProductsPage is suspended.
export default function Loading() {
  return (
    <main>
      <h1>Products</h1>
      <p aria-live="polite">Loading products…</p>
    </main>
  );
}
```

```tsx
// app/products/error.tsx
// Must be a Client Component so it can use the `reset` callback.
'use client';

import { useEffect } from 'react';

type ErrorProps = {
  error: Error & { digest?: string };
  reset: () => void;
};

export default function Error({ error, reset }: ErrorProps) {
  useEffect(() => {
    // Log to an error-reporting service in production
    console.error(error);
  }, [error]);

  return (
    <main>
      <h2>Something went wrong loading products.</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </main>
  );
}
```

---

## 4.2 — Server Actions and Mutations

```ts
// app/products/actions.ts
'use server';

import { z } from 'zod';
import { revalidatePath } from 'next/cache';

// ── Zod schema ────────────────────────────────────────────────────────────────

const CreateProductSchema = z.object({
  name: z.string().min(1, 'Name is required').max(200),
  price: z.coerce.number().positive('Price must be positive'),
  stock: z.coerce.number().int().nonnegative().optional(),
});

// ── Return type — discriminated union ─────────────────────────────────────────

type ProductRecord = { id: string; name: string; price: number; stock?: number };

type ActionSuccess = { ok: true; product: ProductRecord };
type ActionError   = { ok: false; error: string; fieldErrors?: Record<string, string[]> };
type ActionResult  = ActionSuccess | ActionError;

// ── Mock DB layer ─────────────────────────────────────────────────────────────

async function insertProduct(
  data: z.infer<typeof CreateProductSchema>,
): Promise<ProductRecord> {
  // Replace with a real DB call (Prisma, Drizzle, etc.)
  return { id: crypto.randomUUID(), ...data };
}

// ── Server Action ─────────────────────────────────────────────────────────────

export async function createProduct(formData: FormData): Promise<ActionResult> {
  const raw = {
    name:  formData.get('name'),
    price: formData.get('price'),
    stock: formData.get('stock') ?? undefined,
  };

  const parsed = CreateProductSchema.safeParse(raw);

  if (!parsed.success) {
    return {
      ok: false,
      error: 'Validation failed',
      fieldErrors: parsed.error.flatten().fieldErrors as Record<string, string[]>,
    };
  }

  try {
    const product = await insertProduct(parsed.data);
    revalidatePath('/products');
    return { ok: true, product };
  } catch {
    return { ok: false, error: 'Database error — please try again.' };
  }
}
```

```tsx
// app/products/create-product-form.tsx
'use client';

import { useActionState } from 'react';
import { createProduct } from './actions';

type FormState = Awaited<ReturnType<typeof createProduct>> | null;

export default function CreateProductForm() {
  const [state, formAction, isPending] = useActionState<FormState, FormData>(
    (_prev, formData) => createProduct(formData),
    null,
  );

  return (
    <form action={formAction} noValidate>
      <div>
        <label htmlFor="name">Name</label>
        <input id="name" name="name" type="text" required />
        {/* The client narrows the type via the `ok` discriminant — no assertions needed */}
        {state && !state.ok && state.fieldErrors?.name && (
          <span role="alert">{state.fieldErrors.name[0]}</span>
        )}
      </div>

      <div>
        <label htmlFor="price">Price</label>
        <input id="price" name="price" type="number" step="0.01" min="0.01" required />
        {state && !state.ok && state.fieldErrors?.price && (
          <span role="alert">{state.fieldErrors.price[0]}</span>
        )}
      </div>

      <div>
        <label htmlFor="stock">Stock (optional)</label>
        <input id="stock" name="stock" type="number" step="1" min="0" />
      </div>

      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating…' : 'Create Product'}
      </button>

      {/* Non-field error */}
      {state && !state.ok && !state.fieldErrors && (
        <p role="alert">{state.error}</p>
      )}

      {/* Success confirmation */}
      {state?.ok && (
        <p role="status">Product "{state.product.name}" created successfully!</p>
      )}
    </form>
  );
}
```

---

## 4.3 — Middleware

```ts
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

const RATE_LIMIT_MAX        = 60;
const RATE_LIMIT_WINDOW_MS  = 60_000;

type RateLimitState = { count: number; windowStart: number };

export function middleware(request: NextRequest): NextResponse {
  const { pathname } = request.nextUrl;
  const response = NextResponse.next();

  // ── 1. Auth guard for /dashboard/* ─────────────────────────────────────────
  if (pathname.startsWith('/dashboard')) {
    const token = request.cookies.get('auth-token')?.value;
    if (!token) {
      const loginUrl = new URL('/login', request.url);
      loginUrl.searchParams.set('from', pathname);
      return NextResponse.redirect(loginUrl);
    }
  }

  // ── 2. Unique request-id header for distributed tracing ────────────────────
  response.headers.set('x-request-id', crypto.randomUUID());

  // ── 3. Cookie-based rate limiting ──────────────────────────────────────────
  const now       = Date.now();
  const raw       = request.cookies.get('rl_state')?.value;
  let count       = 1;
  let windowStart = now;

  if (raw) {
    try {
      const parsed = JSON.parse(raw) as RateLimitState;
      if (now - parsed.windowStart < RATE_LIMIT_WINDOW_MS) {
        count       = parsed.count + 1;
        windowStart = parsed.windowStart;
      }
    } catch {
      // Malformed cookie — reset to a fresh window
    }
  }

  if (count > RATE_LIMIT_MAX) {
    return new NextResponse('Too Many Requests', { status: 429 });
  }

  response.cookies.set(
    'rl_state',
    JSON.stringify({ count, windowStart } satisfies RateLimitState),
    { httpOnly: true, sameSite: 'strict', maxAge: Math.ceil(RATE_LIMIT_WINDOW_MS / 1000) },
  );

  return response;
}

export const config = {
  // Apply to all routes except Next.js internals and static files
  matcher: ['/((?!_next/static|_next/image|favicon.ico).*)'],
};
```

> Rate limiting explanation: This approach is fundamentally flawed in serverless/edge environments because each function instance (or edge replica) has no shared memory — a client that hits different instances can reset its counter on each one, easily exceeding the intended limit. Additionally, cookies are client-controlled: a malicious caller can simply omit or forge the `rl_state` cookie to bypass the counter entirely. Production rate limiting requires server-side storage keyed on a reliable identifier (e.g. IP address or authenticated user ID) using a shared store such as Redis via Upstash or a Durable Object.
