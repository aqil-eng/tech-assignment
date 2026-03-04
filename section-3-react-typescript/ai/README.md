# Section 3 — React and TypeScript (AI Assisted)

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 3.1 — Custom Hook

```ts
import { useState, useEffect } from 'react';

function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      // Avoid an unnecessary re-render if the value hasn't changed
      setDebouncedValue(prev => (Object.is(prev, value) ? prev : value));
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// ── Usage example ────────────────────────────────────────────────────────────

import { useState, useEffect } from 'react';

type SearchResult = { id: number; title: string };

const SearchComponent = () => {
  const [search, setSearch] = useState('');
  const debouncedSearch = useDebounce(search, 500);
  const [results, setResults] = useState<SearchResult[]>([]);

  useEffect(() => {
    if (!debouncedSearch) return;

    const controller = new AbortController();

    fetch(`/api/search?q=${encodeURIComponent(debouncedSearch)}`, {
      signal: controller.signal,
    })
      .then(res => res.json())
      .then((data: SearchResult[]) => setResults(data))
      .catch(err => {
        if (err.name !== 'AbortError') console.error(err);
      });

    // Cancel the in-flight request if the debounced value changes before it resolves
    return () => controller.abort();
  }, [debouncedSearch]);

  return (
    <>
      <input
        type="text"
        value={search}
        onChange={e => setSearch(e.target.value)}
        placeholder="Search…"
      />
      <ul>
        {results.map(r => (
          <li key={r.id}>{r.title}</li>
        ))}
      </ul>
    </>
  );
};
```

---

## 3.2 — Component Design

```tsx
import { useState, useMemo } from 'react';

// T must be an object that contains a unique `id` field
type WithId = { id: string | number };

type Column<T> = {
  key: keyof T;
  label: string;
  render?: (value: T[keyof T], row: T) => React.ReactNode;
  sortValue?: (row: T) => string | number | boolean;
};

type SortState<T> = {
  key: keyof T;
  direction: 'asc' | 'desc';
} | null;

type DataTableProps<T extends WithId> = {
  rows: T[];
  columns: Column<T>[];
  onRowClick?: (row: T) => void;
};

function DataTable<T extends WithId>({
  rows,
  columns,
  onRowClick,
}: DataTableProps<T>) {
  const [sort, setSort] = useState<SortState<T>>(null);

  const handleHeaderClick = (key: keyof T) => {
    setSort(prev => {
      if (prev?.key === key) {
        return { key, direction: prev.direction === 'asc' ? 'desc' : 'asc' };
      }
      return { key, direction: 'asc' };
    });
  };

  const sortedRows = useMemo(() => {
    if (!sort) return rows;

    const col = columns.find(c => c.key === sort.key);
    const getValue = col?.sortValue ?? ((row: T) => row[sort.key] as string | number | boolean);

    // Stable sort: Array.prototype.sort is stable in modern engines (ES2019+),
    // but we preserve the original index as a tiebreaker to be explicit.
    return [...rows]
      .map((row, index) => ({ row, index }))
      .sort((a, b) => {
        const aVal = getValue(a.row);
        const bVal = getValue(b.row);
        if (aVal < bVal) return sort.direction === 'asc' ? -1 : 1;
        if (aVal > bVal) return sort.direction === 'asc' ? 1 : -1;
        return a.index - b.index; // tie → preserve original order
      })
      .map(({ row }) => row);
  }, [rows, sort, columns]);

  return (
    <table>
      <thead>
        <tr>
          {columns.map(col => (
            <th
              key={String(col.key)}
              onClick={() => handleHeaderClick(col.key)}
              style={{ cursor: 'pointer', userSelect: 'none' }}
            >
              {col.label}
              {sort?.key === col.key ? (sort.direction === 'asc' ? ' ▲' : ' ▼') : ' ⇅'}
            </th>
          ))}
        </tr>
      </thead>
      <tbody>
        {sortedRows.map(row => (
          <tr
            key={row.id}
            onClick={() => onRowClick?.(row)}
            style={{ cursor: onRowClick ? 'pointer' : 'default' }}
          >
            {columns.map(col => (
              <td key={String(col.key)}>
                {col.render
                  ? col.render(row[col.key], row)
                  : String(row[col.key] ?? '')}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

// ── Usage example ────────────────────────────────────────────────────────────

type Product = {
  id: number;
  name: string;
  price: number;
  stock: number;
};

const columns: Column<Product>[] = [
  { key: 'name', label: 'Product' },
  {
    key: 'price',
    label: 'Price',
    render: value => `$${(value as number).toFixed(2)}`,
    sortValue: row => row.price,
  },
  { key: 'stock', label: 'Stock' },
];

const products: Product[] = [
  { id: 1, name: 'Widget A', price: 9.99, stock: 100 },
  { id: 2, name: 'Gadget B', price: 24.99, stock: 5 },
  { id: 3, name: 'Doohickey C', price: 4.5, stock: 250 },
];

export default function App() {
  return (
    <DataTable
      rows={products}
      columns={columns}
      onRowClick={row => console.log('Clicked:', row)}
    />
  );
}
```
