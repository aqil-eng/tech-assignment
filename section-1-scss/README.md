# Section 1 — SCSS
## Shared Tokens (Use these throughout)

```scss
$tokens: (
  gutter: 24px,
  colors: (
    primary: #1d4ed8,
    danger: #dc2626,
    muted: #6b7280
  ),
  breakpoints: (
    sm: 640px,
    md: 768px,
    lg: 1024px
  )
);
```

---

## 1.1 — Mixins and Functions

Write an SCSS mixin called `responsive-grid` that accepts:

- `$columns` (number)
- `$gutter` (length)
- `$breakpoint` (optional: length OR breakpoint token key like `md`)

It should generate a CSS Grid layout that:

- Uses `$columns` above the breakpoint
- Collapses to a single column below the breakpoint
- Applies gutters using `gap`

Include a complementary SCSS function called `grid-width` that calculates the width of a single column as a percentage, accounting for gutters.

**Additional Requirements**
- If `$breakpoint` is not provided, default to the `$tokens.breakpoints.md` value.
- If `$breakpoint` is passed as a key (`md`, `lg`), resolve it from `$tokens`.
- `grid-width()` must return a percentage rounded to **4 decimal places**.

```scss
// Write your mixin and function here
```

---

## 1.2 — Architecture and Nesting

The following SCSS compiles but is considered poorly written.

1. Identify **at least four** specific problems (be explicit).
2. Rewrite it following best practices:
   - BEM naming
   - max **2 levels of nesting**
   - use of variables/maps
   - improved maintainability

```scss
.page {
  div {
    background: #ff0000;
    .page_title {
      font-size: 30px;
      color: #ff0000;
      span {
        font-size: 12px;
        a {
          color: blue;
          &:hover {
            color: blue;
          }
        }
      }
    }
    .page_title.active {
      color: green;
    }
  }
}
```

**Problems identified:**
> 1.
> 2.
> 3.
> 4.

**Rewritten SCSS:**
```scss
// Write your improved SCSS here
```
