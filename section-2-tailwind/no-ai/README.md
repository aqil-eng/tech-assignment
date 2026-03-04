# Section 2 — Tailwind CSS (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions.

---

## 2.1 — Utility Composition

```tsx
// Write JSX/HTML here
<div className='max-w-[400px] hover:shadow-xl'>
    {/* Image Section */}
    <div className='h-[100px] relative'>
        <img className='absolute'/>
    </div>

    {/* Content section */}
    <div className='flex flex-row gap-2'>
        <div className='text-[32px] font-bold'>This is Title</div>
        <div className=''>Description</div>
        {/* Button */}
        <button className='bg-blue-400 rounded-xl px-4 py-2 hover:bg-blue-200'>
            Button
        </button>
    </div>
</div>
```

---

## 2.2 — Tailwind Configuration

```ts
// Write your tailwind.config.ts snippet here
/** @type {import('tailwindcss').Config} */

@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

const defaultTheme = require('tailwindcss/defaultTheme');

module.exports = {
  content: ['./src/**/*.{html,js}'],
  theme: {
    colors: {
      brand: {
        '50': '#xxx'
        '100': '#xxx'
        '200': '#xxx'
        '300': '#xxx'
        '400': '#xxx'
        '500': '#xxx'
        '600': '#xxx'
        '700': '#xxx'
        '800': '#xxx'
        '900': '#xxx'
      }
    },
    fontFamily: {
      sans: ['display', ...defaultTheme.fontFamily.sans],
    },
    extend: {
      spacing: {
        '8xl': '96rem',
        '9xl': '128rem',
      },
      borderRadius: {
        '4xl': '2rem',
      }
    }
  },
  plugins: [
    plugin(function({ addUtilities, addComponents, theme, e }) {
      const newUtilities = {
        '.text-balance': {
          'text-wrap': 'balance',
        },
      };
      addUtilities(newUtilities);
    }),
  ],
}
```
