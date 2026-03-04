# Section 7 — Shopify (Optional)

This section is optional. Completing it demonstrates additional expertise in Shopify ecosystem development.

---

## 7.1 — Liquid Templating

Write a Liquid snippet for a product page that:

1. Renders the product's image gallery with responsive `srcset` attributes using Shopify's image CDN filters
2. Displays a variant picker that updates the price and availability dynamically using a `<script type="application/json">` block to serialize variant data
3. Handles the case where a product has no available variants gracefully (showing a "Sold Out" state with a back-in-stock notification form)
4. Uses Liquid's `{% render %}` tag to include a reusable component, and explain why `{% render %}` is preferred over `{% include %}`

**Additional Requirement**
- Mention 1 performance consideration (lazy loading, responsive widths, etc.)

```liquid
{%- comment -%}
// Write your Liquid here
{%- endcomment -%}
```

> Performance consideration:

---

## 7.2 — Theme Development

Answer the following:

**1. Describe the structure of a Shopify Online Store 2.0 theme. What are sections, blocks, and app extensions, and how do they relate via the `{% schema %}` tag?**
> Answer:

**2. You need to add a custom metafield-driven content section (e.g., FAQs) to a product page. Walk through the full process: defining the metafield, creating the section schema, and rendering the data in Liquid.**
> Answer:

**3. Explain how `settings_schema.json` differs from a section's `{% schema %}`, and when you would use each.**
> Answer:

---

## 7.3 — Custom Storefront / Headless

Write a code example (React + TypeScript) that uses the Shopify Storefront API (GraphQL) to:

1. Fetch the first 12 products from a collection, including title, price, handle, and the featured image URL
2. Implement cursor-based pagination to load more products
3. Use proper TypeScript types generated from or modelled after the Storefront API schema

Briefly explain:
- When you would choose a headless/Hydrogen approach over a traditional Liquid theme
- Two trade-offs of going headless (one operational, one commerce-feature related)

```tsx
// Write your React + TS example here
```

> Headless vs Liquid:

> Trade-offs:
