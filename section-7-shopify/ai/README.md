# Section 7 — Shopify (AI Assisted) — Optional

**This is Pass 2.** You may use AI tools to complete or improve your answers.

- **Permitted model:** Claude Sonnet only
- **Other AI tools (ChatGPT, Gemini, Copilot, etc.) are not permitted**

Refer to [../README.md](../README.md) for the full questions.

---

## 7.1 — Liquid Templating

```liquid
{%- comment -%}
  snippets/product-main.liquid
  Renders: image gallery, variant picker, sold-out state, and back-in-stock form.
  Uses {% render %} for the reusable back-in-stock component.
{%- endcomment -%}

{%- assign current_variant = product.selected_or_first_available_variant -%}

{%- comment -%} Check whether every variant is sold out {%- endcomment -%}
{%- assign all_sold_out = true -%}
{%- for variant in product.variants -%}
  {%- if variant.available -%}
    {%- assign all_sold_out = false -%}
    {%- break -%}
  {%- endif -%}
{%- endfor -%}

<section class="product" data-product-id="{{ product.id }}">

  {%- comment -%} ── Image Gallery ──────────────────────────────────────────── {%- endcomment -%}
  <div class="product__gallery">
    {%- for image in product.images -%}
      <img
        src="{{ image | image_url: width: 800 }}"
        srcset="
          {{ image | image_url: width: 400  }} 400w,
          {{ image | image_url: width: 800  }} 800w,
          {{ image | image_url: width: 1200 }} 1200w
        "
        sizes="(max-width: 768px) 100vw, 50vw"
        alt="{{ image.alt | escape }}"
        width="{{ image.width }}"
        height="{{ image.height }}"
        {%- comment -%} Eagerly load the hero image; lazy-load the rest {%- endcomment -%}
        loading="{{ forloop.first | default: false | if: 'eager', 'lazy' }}"
      >
    {%- else -%}
      {{ 'product-1' | placeholder_svg_tag: 'product__gallery-placeholder' }}
    {%- endfor -%}
  </div>

  {%- comment -%}
    Serialize all variant data into a <script type="application/json"> block.
    JavaScript reads this block to update price and availability on variant
    selection without an additional network request.
  {%- endcomment -%}
  <script type="application/json" id="ProductJSON-{{ product.id }}">
    {
      "id": {{ product.id | json }},
      "variants": [
        {%- for variant in product.variants -%}
          {
            "id":        {{ variant.id        | json }},
            "title":     {{ variant.title     | json }},
            "price":     {{ variant.price     | json }},
            "available": {{ variant.available | json }},
            "options":   {{ variant.options   | json }}
          }{%- unless forloop.last -%},{%- endunless -%}
        {%- endfor -%}
      ]
    }
  </script>

  <div class="product__info">
    <h1 class="product__title">{{ product.title | escape }}</h1>

    {%- comment -%} Price — updated by JS when the variant changes {%- endcomment -%}
    <p class="product__price" data-price>
      {{ current_variant.price | money }}
    </p>

    {%- if all_sold_out -%}
      {%- comment -%} ── Sold-out state ──────────────────────────────────── {%- endcomment -%}
      <p class="product__badge product__badge--sold-out">Sold Out</p>

      {%- comment -%}
        {% render %} is preferred over {% include %} because it creates a
        completely isolated scope — the rendered snippet cannot read or mutate
        variables from the parent template. This prevents accidental variable
        leakage, makes snippets predictable and reusable, and aligns with
        Shopify's recommended OS 2.0 patterns. Variables must be passed
        explicitly, which also serves as self-documentation of the interface.
      {%- endcomment -%}
      {%- render 'back-in-stock-form', product: product -%}

    {%- else -%}
      {%- comment -%} ── Variant picker ──────────────────────────────────── {%- endcomment -%}
      {%- unless product.has_only_default_variant -%}
        <form class="product__variant-form" data-product-form>
          {%- for option in product.options_with_values -%}
            <div class="product__option">
              <label for="Option-{{ section.id }}-{{ forloop.index }}">
                {{ option.name }}
              </label>
              <select
                id="Option-{{ section.id }}-{{ forloop.index }}"
                name="options[{{ option.name | escape }}]"
                data-option-index="{{ forloop.index0 }}"
              >
                {%- for value in option.values -%}
                  <option
                    value="{{ value | escape }}"
                    {%- if option.selected_value == value %} selected{%- endif -%}
                  >
                    {{ value | escape }}
                  </option>
                {%- endfor -%}
              </select>
            </div>
          {%- endfor -%}

          <button
            type="submit"
            class="product__add-to-cart"
            data-add-to-cart
            {%- unless current_variant.available %} disabled{%- endunless -%}
          >
            {%- if current_variant.available -%}
              Add to Cart
            {%- else -%}
              Unavailable
            {%- endif -%}
          </button>
        </form>
      {%- endunless -%}
    {%- endif -%}
  </div>

</section>
```

> Performance consideration: All gallery images beyond the first use `loading="lazy"` so the browser only fetches them when they scroll into view. The `srcset` attribute with Shopify CDN URLs allows the browser to request the smallest image that satisfies the viewport width, avoiding unnecessary bandwidth on mobile. For the hero image, `loading="eager"` (the default) and `fetchpriority="high"` (can be added) ensure it is not delayed by the lazy-loading heuristic.

---

## 7.2 — Theme Development

**1. Describe the structure of a Shopify Online Store 2.0 theme. What are sections, blocks, and app extensions, and how do they relate via the `{% schema %}` tag?**
> A Shopify OS 2.0 theme is built around **JSON templates** in `templates/` that list an ordered set of sections (replacing the older, hardcoded Liquid templates). A **section** is a self-contained, merchant-configurable Liquid file in `sections/` that exposes its controls through a `{% schema %}` tag — a JSON descriptor declaring the section's name, settings (rendered as editor controls whose values flow into Liquid as `section.settings.*`), permitted **block** types, and placement presets. **Blocks** are sub-components within a section (e.g., individual FAQ items inside an accordion section); each block type has its own settings declared in the schema's `"blocks"` array, and their data is iterable in Liquid via `section.blocks`. **App extensions** (registered with the Shopify CLI) are sections or blocks that an app injects into the theme editor as first-class, draggable components, letting apps add storefront UI without patching theme files directly. The `{% schema %}` tag is the contract between code and the editor: it determines what controls merchants see, what data they can configure, and which templates a section may appear on.

**2. You need to add a custom metafield-driven content section (e.g., FAQs) to a product page. Walk through the full process: defining the metafield, creating the section schema, and rendering the data in Liquid.**
> **Step 1 — Define the metafield**: In *Shopify Admin → Settings → Custom data → Products*, add a new definition with namespace `custom`, key `faqs`, type *List of metaobjects* (or *JSON* for a simpler approach). If using metaobjects, first create a `faq` metaobject definition with `question` (single-line text) and `answer` (multi-line text) fields.
>
> **Step 2 — Populate the metafield**: Open any product and fill in the FAQ entries using the new field in the product detail page.
>
> **Step 3 — Create the section** (`sections/product-faqs.liquid`):
> ```liquid
> {%- assign faqs = product.metafields.custom.faqs.value -%}
> {%- if faqs and faqs.size > 0 -%}
>   <section class="product-faqs">
>     <h2>{{ section.settings.heading }}</h2>
>     {%- for faq in faqs -%}
>       <details>
>         <summary>{{ faq.question | escape }}</summary>
>         <p>{{ faq.answer | escape }}</p>
>       </details>
>     {%- endfor -%}
>   </section>
> {%- endif -%}
>
> {% schema %}
> {
>   "name": "Product FAQs",
>   "settings": [
>     { "type": "text", "id": "heading", "label": "Heading", "default": "FAQs" }
>   ],
>   "presets": [{ "name": "Product FAQs" }]
> }
> {% endschema %}
> ```
>
> **Step 4 — Add it to the template**: In the theme editor, open a product template and insert the *Product FAQs* section from the section picker.

**3. Explain how `settings_schema.json` differs from a section's `{% schema %}`, and when you would use each.**
> `settings_schema.json` (in `config/`) defines **global theme settings** that appear in the *Theme settings* panel and are available as `settings.*` everywhere in the theme — things like brand colours, typography scales, and default spacing that should apply uniformly across the entire storefront. A section's `{% schema %}` tag scopes settings and blocks to **that specific section instance**: values are stored per-placement in the JSON template file and accessed via `section.settings.*`, so two instances of the same section on the same page can have different values. Use `settings_schema.json` for design tokens and global defaults that need to be consistent everywhere; use a section's `{% schema %}` for content, layout, and behaviour controls that belong to a particular, independently configurable piece of the page.

---

## 7.3 — Custom Storefront / Headless

```tsx
// hooks/useCollection.ts + components/ProductCollection.tsx

// ── Types modelled after the Storefront API schema ────────────────────────────

type Money = {
  amount: string;
  currencyCode: string;
};

type Image = {
  url: string;
  altText: string | null;
  width: number;
  height: number;
};

type ProductNode = {
  id: string;
  title: string;
  handle: string;
  featuredImage: Image | null;
  priceRange: {
    minVariantPrice: Money;
  };
};

type PageInfo = {
  hasNextPage: boolean;
  endCursor: string | null;
};

type CollectionProductsResponse = {
  collection: {
    products: {
      edges: Array<{ cursor: string; node: ProductNode }>;
      pageInfo: PageInfo;
    };
  };
};

// ── GraphQL query ─────────────────────────────────────────────────────────────

const COLLECTION_PRODUCTS_QUERY = /* GraphQL */ `
  query CollectionProducts($handle: String!, $first: Int!, $after: String) {
    collection(handle: $handle) {
      products(first: $first, after: $after) {
        edges {
          cursor
          node {
            id
            title
            handle
            featuredImage {
              url
              altText
              width
              height
            }
            priceRange {
              minVariantPrice {
                amount
                currencyCode
              }
            }
          }
        }
        pageInfo {
          hasNextPage
          endCursor
        }
      }
    }
  }
`;

// ── Storefront API fetch helper ───────────────────────────────────────────────

async function storefrontFetch<TData>(
  query: string,
  variables: Record<string, unknown>,
): Promise<TData> {
  const endpoint = `https://${process.env.NEXT_PUBLIC_SHOPIFY_DOMAIN}/api/2024-01/graphql.json`;

  const res = await fetch(endpoint, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Shopify-Storefront-Access-Token':
        process.env.NEXT_PUBLIC_SHOPIFY_STOREFRONT_TOKEN ?? '',
    },
    body: JSON.stringify({ query, variables }),
  });

  if (!res.ok) {
    throw new Error(`Storefront API HTTP error: ${res.status}`);
  }

  const json = (await res.json()) as { data: TData; errors?: unknown[] };

  if (json.errors?.length) {
    throw new Error(`GraphQL errors: ${JSON.stringify(json.errors)}`);
  }

  return json.data;
}

// ── Component ─────────────────────────────────────────────────────────────────

import { useState, useEffect, useCallback } from 'react';

type Props = {
  collectionHandle: string;
};

export default function ProductCollection({ collectionHandle }: Props) {
  const [products, setProducts] = useState<ProductNode[]>([]);
  const [pageInfo, setPageInfo] = useState<PageInfo>({
    hasNextPage: false,
    endCursor: null,
  });
  const [loading, setLoading] = useState(false);
  const [error, setError]     = useState<string | null>(null);

  const fetchProducts = useCallback(
    async (cursor: string | null = null) => {
      setLoading(true);
      setError(null);
      try {
        const data = await storefrontFetch<CollectionProductsResponse>(
          COLLECTION_PRODUCTS_QUERY,
          { handle: collectionHandle, first: 12, after: cursor },
        );
        const { edges, pageInfo: pi } = data.collection.products;
        const nodes = edges.map(e => e.node);
        // Append to existing list when paginating; replace for initial load
        setProducts(prev => (cursor ? [...prev, ...nodes] : nodes));
        setPageInfo(pi);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'An unexpected error occurred.');
      } finally {
        setLoading(false);
      }
    },
    [collectionHandle],
  );

  // Initial fetch
  useEffect(() => {
    void fetchProducts(null);
  }, [fetchProducts]);

  if (error) {
    return <p role="alert">Error: {error}</p>;
  }

  return (
    <div>
      <div className="product-grid">
        {products.map(product => (
          <article key={product.id} className="product-card">
            {product.featuredImage && (
              <img
                src={product.featuredImage.url}
                alt={product.featuredImage.altText ?? product.title}
                width={product.featuredImage.width}
                height={product.featuredImage.height}
              />
            )}
            <h2>{product.title}</h2>
            <p>
              {Number(product.priceRange.minVariantPrice.amount).toFixed(2)}{' '}
              {product.priceRange.minVariantPrice.currencyCode}
            </p>
            <a href={`/products/${product.handle}`}>View product</a>
          </article>
        ))}
      </div>

      {/* Cursor-based pagination — load the next page using the endCursor */}
      {pageInfo.hasNextPage && (
        <button
          onClick={() => void fetchProducts(pageInfo.endCursor)}
          disabled={loading}
        >
          {loading ? 'Loading…' : 'Load more'}
        </button>
      )}

      {loading && products.length === 0 && <p>Loading products…</p>}
    </div>
  );
}
```

> Headless vs Liquid: Choose a headless/Hydrogen approach when you need a fully custom, highly interactive frontend that goes beyond what Liquid templates can ergonomically support — for example a React-driven SPA with complex client-side state, a cross-platform storefront that shares components with a native mobile app, or when your engineering team is React-native and Liquid's rendering model is a significant productivity bottleneck.

> Trade-offs:
> - **Operational**: You take on the full infrastructure burden of hosting, deploying, and maintaining a separate web application — edge hosting configuration, CDN rules, build pipelines, and incident response — rather than relying on Shopify's fully-managed, globally-distributed CDN and hosting that Liquid themes get for free. This increases operational complexity and cost.
> - **Commerce features**: Several out-of-the-box Shopify features are tightly coupled to the Liquid layer — native Shopify Markets locale/currency-switching UI, checkout extensibility via checkout.liquid, and certain B2B storefront controls — and require significant additional integration effort or are unavailable entirely in a headless setup, meaning your team must re-implement functionality that traditional Liquid themes provide without any custom code.
