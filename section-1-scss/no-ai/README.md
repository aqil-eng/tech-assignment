# Section 1 — SCSS (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions and shared tokens.

---

## 1.1 — Mixins and Functions

```scss
// Write your mixin and function here
@function get-token($target) {
    $res: $token;
    @each $key in $target {
        $res: map-get($res, $key);
    }
    return $res;
}

@function grid-width($columns, $gutter) {
    $gutter-space: $gutter * ($columns -1)
    $available-space: 100% - $gutter-space
    $column-width: $available-space/$columns
    return $column-width;
}



```

---

## 1.2 — Architecture and Nesting

**Problems identified:**
> 1. Heavily nested structure
> 2. Colors should be defined as constant not hard-coded
> 3. Inappropriate assignment to html element
> 4. Redundant hover functionality

**Rewritten SCSS:**
```scss
// Write your improved SCSS here
$colors: (
    primary: map-get($tokens, colors, primary)
    danger: map-get($tokens, colors, primary)
    muted: map-get($tokens, colors, primary)
)


```
