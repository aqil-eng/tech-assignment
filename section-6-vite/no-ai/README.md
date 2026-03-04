# Section 6 — Vite and Build Tooling (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions.

---

## 6.1 — Configuration

```ts
// Write vite.config.ts here
```

```json
// Write tsconfig.json snippet here
```

---

## 6.2 — Concepts

**1. How does Vite's dev server differ architecturally from Webpack's, and why is this faster?**
> Answer: Leveraging esbuild as it is so much faster than the Webpack's as it is written in Go which is suitable for parallellism and it uses memory efficiently.

**2. What is the role of Rollup in a Vite production build, and when might you choose esbuild instead?**
> Answer: Choose esbuild when you want to have a faster development pace with minimum configuration and settings. Rollup excels on producing optimized smaller bundles especially for production because it employs various optimization techniques, such as minification, compression, and code splitting, to reduce the size of the generated bundles.

**3. You notice that a third-party library is causing full-page reloads instead of HMR updates. What is the most likely cause, and how would you fix it?**
> Answer: Common causes include exporting non-component code in React (breaking Fast Refresh), circular dependencies, editing configuration files, or losing the WebSocket connection. First thing first is to check console error related to the HMR failures and make sure there is no circular dependencies
