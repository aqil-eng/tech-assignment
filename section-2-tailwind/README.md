# Section 2 — Tailwind CSS
## 2.1 — Utility Composition

Without using `@apply`, build a responsive card component using only Tailwind utility classes (in JSX/HTML).

**Requirements**
- Max width of `400px`, full width on mobile
- A top image section with a `16:9` aspect ratio
  *(do not use `aspect-video`; implement manually)*
- A content section with a title, description, and CTA button
- On hover, the card should lift with a subtle shadow transition
- Dark mode support for background and text colours
- CTA must have `focus-visible` ring styling

```tsx
// Write JSX/HTML here
```

---

## 2.2 — Tailwind Configuration

Write a `tailwind.config.ts` snippet that accomplishes all of the following:

1. Extends the default theme with a custom colour palette called `brand` (shades `50` through `900`).
2. Adds a custom `fontFamily` called `display` that falls back to `sans-serif`.
3. Registers a custom plugin that adds a `.text-balance` utility (setting `text-wrap: balance`).
4. Configures content paths appropriate for a Next.js App Router project.
5. Plugin must be written without `any` types.

```ts
// Write your tailwind.config.ts snippet here
```
