# Section 6 — Vite and Build Tooling (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 6.1 — Configuration

```ts
// vite.config.ts
import { defineConfig, loadEnv } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig(({ mode }) => {
  // Load env vars with the VITE_ prefix from the correct .env file
  const env = loadEnv(mode, process.cwd(), '');

  // Validate required env vars at build time so missing vars fail fast
  if (!env.VITE_API_URL) {
    throw new Error(
      '[vite] VITE_API_URL must be defined. ' +
      'Add it to your .env (development) or set it in your CI environment (production).',
    );
  }

  return {
    plugins: [react()],

    // ── Path aliases ─────────────────────────────────────────────────────────
    resolve: {
      alias: {
        '@': path.resolve(__dirname, './src'),
      },
    },

    // ── Dev server proxy ─────────────────────────────────────────────────────
    server: {
      proxy: {
        '/api': {
          target: 'http://localhost:8000',
          changeOrigin: true,
          // Uncomment to strip the /api prefix before forwarding:
          // rewrite: (p) => p.replace(/^\/api/, ''),
        },
      },
    },

    // ── Production build — separate vendor chunks ─────────────────────────────
    build: {
      rollupOptions: {
        output: {
          manualChunks: {
            // react and react-dom land in a single, long-cached vendor chunk
            react: ['react', 'react-dom'],
          },
        },
      },
    },
  };
});
```

```json
// tsconfig.json — compilerOptions snippet
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

---

## 6.2 — Concepts

**1. How does Vite's dev server differ architecturally from Webpack's, and why is this faster?**
> Webpack bundles the entire dependency graph into one or more bundles before the dev server can serve anything, so startup and incremental rebuild times grow linearly with application size. Vite skips the upfront bundle step entirely: it pre-bundles third-party dependencies with esbuild once (which is ~10–100× faster than JavaScript-based bundlers), then serves application source files as native ES modules directly to the browser on demand. Because the browser itself resolves imports, Vite only transforms the single file that was actually requested, making cold starts near-instant and keeping HMR updates scoped to exactly the changed module regardless of project size.

**2. What is the role of Rollup in a Vite production build, and when might you choose esbuild instead?**
> Vite delegates production bundling to Rollup because Rollup produces highly optimised output: it performs deep tree-shaking by analysing static import/export graphs, supports rich code-splitting and chunk-naming strategies, and has a mature plugin ecosystem for advanced transformations. You would choose esbuild for production when raw build speed is the primary constraint — for example in large monorepo CI pipelines, library builds with simple single-file output, or internal tooling where bundle size and tree-shaking precision matter less than how quickly the artefact is produced.

**3. You notice that a third-party library is causing full-page reloads instead of HMR updates. What is the most likely cause, and how would you fix it?**
> The most likely cause is that the library's module exports both React components and side-effectful non-component code (plain objects, event listeners, imperative initialisers) from the same file, which breaks Vite's Fast Refresh boundary detection — React Fast Refresh requires that every export in a file be a React component. To fix it, first check the browser console for a message like *"[vite] full reload"* and the HMR error that precedes it. If the library cannot be changed, you can isolate the import into a thin wrapper module that re-exports only the component, add the library to `optimizeDeps.include` in `vite.config.ts` so Vite pre-bundles it as a single CJS chunk (avoiding the ESM boundary issue), or — if the library emits no HMR events at all — add a `// @vite-ignore` comment and handle updates manually with `import.meta.hot.accept()`.
