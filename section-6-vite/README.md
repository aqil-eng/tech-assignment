# Section 6 — Vite and Build Tooling
## 6.1 — Configuration

Write a `vite.config.ts` for a React + TypeScript project that:

1. Configures path aliases so `@/components/*` maps to `./src/components/*`
2. Sets up a proxy for `/api` requests to `http://localhost:8000` during development
3. Configures the build to output separate vendor chunks for `react` and `react-dom`
4. Adds environment variable validation that ensures `VITE_API_URL` is defined at build time

**Additional Requirement**
- Include the matching `tsconfig.json` `compilerOptions.paths` snippet.

```ts
// Write vite.config.ts here
```

```json
// Write tsconfig.json snippet here
```

---

## 6.2 — Concepts

Answer the following in 2–3 sentences each:

**1. How does Vite's dev server differ architecturally from Webpack's, and why is this faster?**
> Answer:

**2. What is the role of Rollup in a Vite production build, and when might you choose esbuild instead?**
> Answer:

**3. You notice that a third-party library is causing full-page reloads instead of HMR updates. What is the most likely cause, and how would you fix it?**
> Answer:
