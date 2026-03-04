# Section 3 — React and TypeScript
## 3.1 — Custom Hook

Write a custom React hook called `useDebounce<T>` in TypeScript.

It should:
- Accept a `value` of generic type `T` and a `delay` in milliseconds
- Return the debounced value
- Properly clean up timers on unmount or when inputs change
- Be fully typed with no use of `any`

**Additional Requirement**
- Avoid unnecessary renders (do not set state if the value hasn't changed).

Then write a short example showing how you would use this hook to debounce a search input and trigger an API call via `useEffect`.

```ts
// Write your hook + usage example here
```

---

## 3.2 — Component Design

Design and implement a fully typed `<DataTable<T>>` React component in TypeScript that meets these requirements:

- Accepts a generic array of data (`rows: T[]`)
- Accepts a `columns` configuration array that maps keys of `T` to:
  - display label
  - optional `render` function
  - optional `sortValue` function
- Supports client-side sorting by clicking column headers (toggle ascending/descending)
- Sorting must be **stable** (if values tie, preserve original order)
- Includes an `onRowClick` callback that returns the clicked row's data, fully typed
- Uses appropriate generic constraints so that `T` must be an object with a unique `id` field

**You do not need to style it.**

```tsx
// Write types + component implementation here
```
