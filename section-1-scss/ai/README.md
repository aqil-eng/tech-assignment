# Section 1 — SCSS (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions and shared tokens.

---

## 1.1 — Mixins and Functions

```scss
@use 'sass:math';
@use 'sass:map';

// $tokens is defined globally — shown here for reference
$tokens: (
  gutter: 24px,
  colors: (
    primary: #1d4ed8,
    danger:  #dc2626,
    muted:   #6b7280
  ),
  breakpoints: (
    sm: 640px,
    md: 768px,
    lg: 1024px
  )
);

// ---------------------------------------------------------------------------
// grid-width($columns, $gutter, $container)
//
// Returns the width of a single column as a percentage of $container,
// after deducting ($columns - 1) gutters. Rounded to 4 decimal places.
//
// A container reference is required to convert px gutters to a pure
// percentage — defaults to 1200px (common max-width).
// ---------------------------------------------------------------------------
@function grid-width($columns, $gutter, $container: 1200px) {
  $total-gap: $gutter * ($columns - 1);
  $col-px:    math.div($container - $total-gap, $columns);
  $ratio:     math.div($col-px, $container);              // unitless 0–1
  $pct4:      math.div(math.round($ratio * 1000000), 10000); // 4 d.p.
  @return $pct4 * 1%;
}

// ---------------------------------------------------------------------------
// resolve-breakpoint($bp)
//
// Accepts a token key ('sm', 'md', 'lg') or a raw length value.
// Returns the resolved length.
// ---------------------------------------------------------------------------
@function resolve-breakpoint($bp) {
  $bps: map.get($tokens, breakpoints);
  @if map.has-key($bps, $bp) {
    @return map.get($bps, $bp);
  }
  @return $bp;
}

// ---------------------------------------------------------------------------
// responsive-grid($columns, $gutter, $breakpoint)
//
// Generates a CSS Grid layout:
//   - $columns columns above $breakpoint, with $gutter gaps
//   - collapses to a single column below $breakpoint
//
// $breakpoint defaults to $tokens.breakpoints.md when omitted.
// Pass a token key ('sm', 'md', 'lg') or any raw length (e.g. 900px).
// ---------------------------------------------------------------------------
@mixin responsive-grid($columns, $gutter, $breakpoint: null) {
  $bp: if(
    $breakpoint == null,
    map.get(map.get($tokens, breakpoints), md),
    resolve-breakpoint($breakpoint)
  );

  display: grid;
  gap: $gutter;
  grid-template-columns: repeat($columns, 1fr);

  @media (max-width: ($bp - 1px)) {
    grid-template-columns: 1fr;
  }
}

// ---------------------------------------------------------------------------
// Usage examples
// ---------------------------------------------------------------------------

// 3-column grid, collapses below md (768px) using default
.grid-default {
  @include responsive-grid(3, 24px);
  // grid-width(3, 24px) → ((1200 - 48) / 3) / 1200 * 100 = 32%
}

// 4-column grid, collapses below lg using a token key
.grid-lg {
  @include responsive-grid(4, 16px, lg);
}

// 2-column grid, collapses below a raw breakpoint
.grid-custom {
  @include responsive-grid(2, 32px, 900px);
}
```

---

## 1.2 — Architecture and Nesting

**Problems identified:**
> 1. **Deep nesting (4+ levels)**: The rule chain `.page > div > .page_title > span > a` produces overly specific selectors that are hard to override and tightly coupled to the markup structure.
> 2. **Hard-coded colour values**: `#ff0000`, `blue`, and `green` are written inline instead of referencing the shared `$tokens` map, making global colour changes error-prone.
> 3. **Bare element selector (`div`)**: Targeting an anonymous `div` inside `.page` makes the styles fragile — any structural markup change breaks the rule without CSS warning.
> 4. **Incorrect BEM naming**: `.page_title` uses a single underscore (non-standard) instead of the BEM double-underscore element syntax (`.page__title`), and the active variant `.page_title.active` should be a BEM modifier (`.page__title--active`), not a chained class buried inside nesting.
>
> Bonus: The `a:hover` rule sets `color: blue` — identical to the default state — making it a redundant no-op.

**Rewritten SCSS:**
```scss
@use 'sass:map';

$tokens: (
  gutter: 24px,
  colors: (
    primary: #1d4ed8,
    danger:  #dc2626,
    muted:   #6b7280
  ),
  breakpoints: (
    sm: 640px,
    md: 768px,
    lg: 1024px
  )
);

$color-danger:  map.get(map.get($tokens, colors), danger);
$color-primary: map.get(map.get($tokens, colors), primary);
$color-muted:   map.get(map.get($tokens, colors), muted);

// Block
.page {

  // Element: the header band (was the anonymous div)
  &__header {
    background: $color-danger;
  }

  // Element: page title
  &__title {
    font-size: 1.875rem; // 30px — prefer rem over px
    color: $color-danger;

    // Modifier: active state (max 2 levels of nesting)
    &--active {
      color: $color-primary;
    }
  }

  // Element: subtitle text (was the span inside .page_title)
  &__subtitle {
    font-size: 0.75rem; // 12px
  }

  // Element: inline link
  &__link {
    color: $color-primary;

    // Pseudo-class counts as the second nesting level — still within limit
    &:hover {
      color: $color-muted; // was a no-op; now uses a token for a visible state change
    }
  }
}
```
