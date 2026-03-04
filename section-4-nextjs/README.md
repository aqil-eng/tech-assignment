# Section 4 — Next.js (App Router)
## 4.1 — Data Fetching and Caching

You are building a product listing page.

Explain (with code) how you would:

1. Create a **Server Component** that fetches a list of products from an external REST API
2. Implement **ISR** with a 60-second revalidation window
3. Add a **loading state** using the App Router's file-based conventions
4. Handle errors gracefully using the App Router's error boundary conventions

**Additional Requirement**
- You must demonstrate correct use of Next.js caching/revalidation semantics (e.g. `fetch(..., { next: { revalidate } })` or route segment revalidation).

Provide the relevant file structure and code for:

- `page.tsx`
- `loading.tsx`
- `error.tsx`

```txt
// Include file structure here
```

```tsx
// Include page.tsx here
```

```tsx
// Include loading.tsx here
```

```tsx
// Include error.tsx here
```

---

## 4.2 — Server Actions and Mutations

Write a Next.js Server Action that handles a form submission for creating a new product.

The action must:
- Validate the input using `zod`
- Insert the product into a database (you may mock the DB call)
- Revalidate the `/products` path after successful insertion
- Return typed success/error responses

**Additional Requirements**
- The return type must be a discriminated union
- The client must narrow the type without assertions

Show how this action is consumed from a Client Component form.

```ts
// Write the server action here
```

```tsx
// Write the client form here
```

---

## 4.3 — Middleware

Write a `middleware.ts` file that:

1. Redirects unauthenticated users to `/login` for any route under `/dashboard/*`
2. Adds a custom `x-request-id` header (UUID) to every response for tracing
3. Implements a basic rate-limiting check using a header or cookie-based counter (conceptual implementation is fine)

**Additional Requirement**
- Include 1–2 sentences explaining why this rate limiting approach is imperfect in serverless/edge environments.

```ts
// Write middleware.ts here
```

> Rate limiting explanation:
