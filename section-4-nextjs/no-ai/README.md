# Section 4 — Next.js App Router (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions.

---

## 4.1 — Data Fetching and Caching

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

```ts
// Write the server action here
import { z } from 'zod'

const productSchema = z.object({
    name: z.string().min(1, "name is required"),
    price: z.number().min(0, "price is required"),
    description: z.string().min(1, "description required"),
})
```

```tsx
// Write the client form here
```

---

## 4.3 — Middleware

```ts
// Write middleware.ts here
```

> Rate limiting explanation:
