# Section 2 — Tailwind CSS (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 2.1 — Utility Composition

```tsx
// Responsive card component — no @apply, Tailwind utility classes only.
//
// 16:9 aspect ratio is implemented manually:
//   A relative container with `pb-[56.25%]` (= 9/16 × 100%) creates the
//   correct height. The image is then positioned absolutely to fill it.

export function Card() {
  return (
    <div className="
      w-full max-w-[400px]
      rounded-xl overflow-hidden
      border border-gray-200 dark:border-gray-700
      bg-white dark:bg-gray-900
      shadow-sm
      transition-all duration-300
      hover:-translate-y-1 hover:shadow-lg
    ">
      {/* 16:9 image section — manual aspect ratio, no aspect-video */}
      <div className="relative w-full pb-[56.25%]">
        <img
          src="/placeholder.jpg"
          alt="Card image"
          className="absolute inset-0 w-full h-full object-cover"
        />
      </div>

      {/* Content section */}
      <div className="p-5 flex flex-col gap-3">
        <h2 className="text-xl font-bold text-gray-900 dark:text-white">
          Card Title
        </h2>

        <p className="text-sm text-gray-600 dark:text-gray-400">
          This is the card description. It provides context about the
          content shown in the image above.
        </p>

        <button
          className="
            mt-1 self-start
            rounded-lg bg-blue-600 px-4 py-2
            text-sm font-medium text-white
            transition-colors duration-200
            hover:bg-blue-700
            focus-visible:outline-none
            focus-visible:ring-2
            focus-visible:ring-blue-500
            focus-visible:ring-offset-2
            dark:focus-visible:ring-offset-gray-900
          "
        >
          Learn More
        </button>
      </div>
    </div>
  );
}
```

---

## 2.2 — Tailwind Configuration

```ts
import type { Config } from 'tailwindcss';
import plugin from 'tailwindcss/plugin';

const config: Config = {
  // Content paths for Next.js App Router
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './lib/**/*.{js,ts,jsx,tsx,mdx}',
  ],

  darkMode: 'class',

  theme: {
    extend: {
      // 1. Custom `brand` colour palette (shades 50–900)
      colors: {
        brand: {
          50:  '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
      },

      // 2. Custom `display` font family with sans-serif fallback
      fontFamily: {
        display: ['Inter', 'sans-serif'],
      },
    },
  },

  // 3. Plugin that adds `.text-balance` — no `any` types
  plugins: [
    plugin(({ addUtilities }) => {
      addUtilities({
        '.text-balance': {
          'text-wrap': 'balance',
        },
      });
    }),
  ],
};

export default config;
```
