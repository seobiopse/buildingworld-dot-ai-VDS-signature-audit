# BuildingWorld.ai — Raw vs Rendered SEO Audit
**Date:** 15 June 2026 | **Prepared by:** Giriish  
**Method:** Ctrl+U Raw HTML (what Googlebot + all AI crawlers see) vs F12 Rendered HTML (what users see after JS)

---

## Why This Matters

| Crawler | Executes JavaScript? | What It Sees |
|---------|---------------------|--------------|
| **Googlebot Wave 1** | ❌ No | Raw HTML only |
| **Googlebot Wave 2** | ✅ Yes (days–weeks later) | Rendered HTML |
| **Bing / Yahoo** | ❌ No | Raw HTML only |
| **ChatGPT (OAI-SearchBot)** | ❌ No | Raw HTML only |
| **Perplexity (PerplexityBot)** | ❌ No | Raw HTML only |
| **Claude (ClaudeBot)** | ❌ No | Raw HTML only |
| **Gemini (Google-Extended)** | ❌ No | Raw HTML only |
| **Apple Intelligence** | ❌ No | Raw HTML only |
| **Your screenshot (diff tool)** | Shows 877 differences on /products/categories |

> **Every AI model citing your site, every AI Overview, every featured snippet — ALL use Raw HTML only. If it's not in Raw, it doesn't exist for AI.**

---


## Agentic Browsing & SXO (Search/Agent Experience Optimization)

AI agents (Perplexity Comet, ChatGPT Atlas, Claude for Chrome, Google-Agent) do not interact like human browsers. They require structural, semantic, and programmatic accessibility:

1. **WCAG Accessibility Baseline:** Elements like `<html lang="en-IN">` and clear form labels are mandatory for NLP models to read elements. Heading structures must map clean page paths (no duplicate H1s).
2. **Hydration Integrity:** Hydration scripts must execute without CORS or origin errors. Next.js Application Error crashes hide the entire body, blocking vision-based and programmatic agents.
3. **WebMCP declarative APIs:** Integrating W3C WebMCP standards enables AI browsers to read form options directly without executing heavy JS loops.

## Page-by-Page Raw vs Rendered Analysis

---

### 📄 Page 1: Homepage (`/`)
**Raw size:** 367KB | **Rendered size:** 760KB | **Difference: 393KB of JS-only content**

| Check | RAW (Googlebot/AI) | RENDERED (User) | Status |
|-------|-------------------|-----------------|--------|
| Title | BuildingWorld \| Verified Building Products... (75 chars) | Same | ✅ Match |
| H1 | `View Ideas` | `View Ideas` | ⚠️ Wrong H1 (should be about the platform, not a button) |
| JSON-LD schemas | **3 blocks** | **4 blocks** | 🔴 1 schema missing from RAW |
| Product links | 34 | 34 | ✅ Match |
| Image tags | **62** | **108** | ⚠️ 46 images JS-only |
| Canonical | `buildingworld.ai` (no trailing slash) | Same | ✅ Match |
| OG:title | Correct | Same | ✅ Match |
| noindex | False | False | ✅ OK |

**Critical finding:** 1 JSON-LD block present in Rendered but **missing from Raw HTML**. The schema that's missing from Raw = invisible to all AI models. Need to identify which schema block is JS-injected.

**Homepage Raw body text quality:** 
- Schema blocks (Corporation, LocalBusiness, FAQPage, WebSite, BreadcrumbList) ✅ All in Raw
- Navigation links ✅ In Raw
- Product cards, trending items ⚠️ Partially loaded

---

### 📄 Page 2: L0 Products Hub (`/products/categories`)
**Raw size:** 280KB | **Rendered size:** 810KB | **Difference: 530KB = 65% of page is JS-only**

| Check | RAW (Googlebot/AI) | RENDERED (User) | Status |
|-------|-------------------|-----------------|--------|
| Title | BuildingWorld \| Explore Top Building Materials... | Same | ✅ Match |
| **H1** | **ZERO H1 TAGS** | **ZERO H1 TAGS** | 🔴 Missing in BOTH |
| JSON-LD schemas | **3 blocks** | **4 blocks** | 🔴 1 schema JS-only |
| Category links | **33 links** | **34 links** | ✅ Near-complete |
| **Image tags** | **49** | **99** | 🔴 50 images JS-only (50%) |
| Canonical | `/products/categories` | Same | ✅ Match |

**Category links visible in Raw HTML (Googlebot can follow these):**
✅ Bathroom and Sanitaryware, Building Material, Ceiling, Cement and Concrete, Doors and Entrances, and 28 more

**What's missing from Raw (JS-only):**
- 50 additional product/brand images
- 1 schema block
- Some dynamic filter components

**Agentic browsing note:** The Raw body text starts with `self.__next_f.push([1,\"1:HL...` — raw Next.js flight data. AI agents parsing this page see Next.js streaming payload as their first content, not the category names.

---

### 📄 Page 3: L1 Category (`/products/categories/lizpqk-furniture-and-home-decor`)
**Raw size:** 2062KB | **Rendered size:** 2152KB | **Difference: 90KB extra in Rendered**

| Check | RAW (Googlebot/AI) | RENDERED (User) | Status |
|-------|-------------------|-----------------|--------|
| Title | Buy Modern Furniture & Home Decor Online... | Same | ✅ Match |
| H1 count | **6 H1 tags** | **6 H1 tags** | 🔴 Multiple H1 in BOTH |
| H1 text (first) | `Furniture and interior decor` | `Furniture and interior decor` | ✅ Same |
| H1 text (others) | `FILTERS`, `Category`, `Price`... | `undefinedFILTERSundefined`... | 🟡 Raw slightly cleaner |
| JSON-LD schemas | **1 block only** | **3 blocks** | 🔴🔴 CRITICAL: 2 schemas JS-only |
| **Product links** | **2 links** | **3 links** | 🔴🔴 CRITICAL |
| **Image tags** | **23** | **392** | 🔴🔴 CRITICAL: 369 images JS-only (94%) |
| Canonical | `...?category_reference_id=12` | Same | ⚠️ Query param in canonical |

### 🔴🔴 CRITICAL — L1 Category Page: 94% of Product Images Are JavaScript-Only

**Googlebot and ALL AI crawlers see only 23 images** on a page that shows 392 images to users. This means:
- The product catalog grid (the core value of a marketplace) is **completely invisible to Googlebot wave-1**
- All 19,097 furniture products — their images, names, and links — exist only after JS executes
- Google Image Search cannot index these product photos
- AI retrieval bots (Perplexity, Claude, ChatGPT) cannot see any products when crawling this page

**Only 1 of 3 schema blocks is in Raw HTML.** The other 2 (likely including ItemList or FAQPage) are JS-injected — invisible to AI models.

**Only 2 product links in Raw** on a page listing thousands of products:
```
Raw product links found:
  1. /products/3ttx1b-viki-furniture-para-chest-of-2-drawer  
  2. /products/[one other link]
```

---

### 📄 Page 4: Product Detail (`/products/3tzsfu-clarens-teak-and-rattan-study-chair-e2a3c2`)
**Raw size:** 185KB | **Rendered size:** 788KB | **Difference: 603KB = 76% of page is JS-only**

| Check | RAW (Googlebot/AI) | RENDERED (User) | Status |
|-------|-------------------|-----------------|--------|
| Title | Clarens Teak and Rattan Study Chair - Elton Navy | Same | ✅ Match |
| **H1 in Raw** | **`?31,999`** (price only!) | `undefinedundefined? undefined 31,999...` | 🔴🔴 CRITICAL |
| JSON-LD schemas | **3 blocks** | **4 blocks** | 🔴 1 schema JS-only |
| **Product links** | **4** | **15** | 🔴 Related products JS-only |
| **Image tags** | **1** | **154** | 🔴🔴 CRITICAL: 153 images JS-only (99%) |
| Canonical | Clean slug URL | Same | ✅ Match |

### 🔴🔴 CRITICAL — Product Detail: Googlebot Sees Only 1 Image (The Product Has 154)

The raw HTML contains **only 1 img tag** on a product detail page. The product has 154 images (gallery, related products, brand logos) in the rendered version — **all loaded by JavaScript**.

**What Googlebot's first wave reads as the H1:** `?31,999`  
A price string (without even the product name) is the only H1 content visible to Googlebot in wave 1.

**Schema @types visible in Raw:** `Product`, `Offer`, `Brand` only  
Missing from Raw (JS-injected): The 4th schema block (likely `BreadcrumbList` or `ItemList`)

---

## Summary Table: Raw vs Rendered Gap

| Page | Raw Size | Rendered Size | Gap | Images (Raw/Rendered) | Schemas (Raw/Rendered) | H1 in Raw |
|------|----------|---------------|-----|----------------------|----------------------|-----------|
| Homepage | 367KB | 760KB | 393KB | 62 / 108 | 3 / 4 | `View Ideas` ⚠️ |
| L0 Products Hub | 280KB | 810KB | **530KB** | 49 / 99 | 3 / 4 | **NONE** 🔴 |
| L1 Category | 2062KB | 2152KB | 90KB | **23 / 392** | **1 / 3** | 6 H1s (broken) 🔴 |
| Product Detail | 185KB | 788KB | **603KB** | **1 / 154** | 3 / 4 | **`?31,999`** 🔴 |

---

## The Three Layers of Visibility

```
LAYER 1 — RAW HTML (Ctrl+U)
  ├── ✅ Title, meta, canonical, OG tags
  ├── ✅ Homepage schema (Corporation, LocalBusiness, FAQPage, WebSite)
  ├── ✅ L0 category navigation links (33/34)
  ├── ❌ L1 product grid (0 products visible)
  ├── ❌ Product gallery images (1/154 visible)
  └── ❌ H1 with actual product name

LAYER 2 — RENDERED HTML (F12 / Wave-2 Googlebot)
  ├── ✅ All product images (but delayed by weeks for Googlebot)
  ├── ✅ Full product listings
  ├── ✅ Filters and faceted navigation  
  ├── ❌ H1 contains "undefined" strings
  ├── ❌ Multiple H1 tags on category pages
  └── ❌ Product name relegated to H2

LAYER 3 — AGENTIC BROWSING (AI agents with tool-use, e.g. ChatGPT, Claude with web tools)
  ├── ✅ Can fetch Raw HTML and parse schema
  ├── ✅ Can follow category links in Raw
  ├── ❌ Cannot execute JavaScript
  ├── ❌ Raw body text opens with Next.js flight data (self.__next_f.push) not content
  └── ❌ Product catalog completely invisible without JS
```

---

## What Needs Fixing by Priority

### 🔴 Immediate (Affects ALL search + AI visibility)

**1. Product Catalog Requires SSR**  
The L1/L2 product grid must render its product cards in the server-side HTML. Every product card — name, price, image, link — must be present in the Raw HTML.
```
Current: 0 products visible in Raw on L1 category page
Target: All visible products (page 1 = ~24-48 products) in Raw HTML
```

**2. Product Detail Images Must SSR**  
At minimum the primary product image must be in Raw HTML.
```
Current: 1 img tag in Raw (webpack chunk, not product image)
Target: First product image + at least 3-4 gallery images in Raw
```

**3. H1 Must Be SSR and Contain Product Name**  
```
Current Raw H1: "?31,999" (price only)
Target Raw H1: "Clarens Teak and Rattan Study Chair - Elton Navy"
```

**4. L1/L2 Schema Injection Must Move to SSR**  
2 out of 3 schemas on the L1 category page are JS-injected — invisible to all AI models.

### 🟠 High Priority (Affects AEO/GEO specifically)

**5. AEO/GEO Raw Content Issue**  
AI models reading Raw HTML get: `self.__next_f.push([1,\"1:HL...` as the first body text — Next.js streaming payload markup. This is not meaningful content. AI retrieval bots that don't execute JS see the Next.js data transport layer, not the actual page content.

**6. Implement `<noscript>` Fallback Content**  
Add `<noscript>` blocks with key product/category information for no-JS crawlers:
```html
<noscript>
  <div>
    <h1>Furniture and Home Decor — 19,097 Products</h1>
    <p>Browse verified furniture and home decor from 4,100+ brands on BuildingWorld.</p>
    <!-- Minimal product list with links -->
  </div>
</noscript>
```

**7. Next.js Streaming Payload Cleanup for AI Bots**  
Consider implementing a bot-detection middleware that serves a clean SSR-only HTML response to known AI crawlers (GPTBot, ClaudeBot, PerplexityBot) without the Next.js flight data markup.

---

## Agentic Browsing Readiness Assessment

For AI agents with tool-use (e.g. ChatGPT browsing, Perplexity live search, Claude with web tools):

| Page | Can Agent Extract Product List? | Can Agent Extract Prices? | Can Agent Navigate Categories? |
|------|--------------------------------|--------------------------|-------------------------------|
| Homepage | ⚠️ Partial | ❌ No | ✅ Yes (nav links) |
| L0 Products Hub | ✅ Category names visible | ❌ No | ✅ 33 category links |
| L1 Category | 🔴 NO (0 products in Raw) | 🔴 NO | ⚠️ 2 product links only |
| Product Detail | 🔴 NO product images | ⚠️ Price in H1 only | ❌ Only 4 related links |

**Verdict: BuildingWorld.ai is NOT agentic browsing ready.** An AI agent tasked with "find me furniture products on BuildingWorld" would:
1. Navigate to `/products/categories` — sees 33 category names ✅
2. Click to Furniture & Home Decor — sees 0 product listings 🔴
3. Cannot return any specific products to the user

---

## Client-Side Hydration & Rendering Fragility (Next.js Application Error)

A critical indicator of the architectural gap between BuildingWorld's JavaScript-dependent client SPA and Amazon's progressive enhancement model is observed when copying/pasting the rendered code:

* **BuildingWorld's Hydration Crash:** Copying and pasting the fully-rendered HTML into an HTML beautifier, viewer, or local offline file loads the page, but immediately triggers a React/Next.js script execution. The scripts attempt client-side hydration, look for assets relative to the current non-production host, fail to fetch them (404s/CORS), and crash. React unmounts the document body and overlays a generic Next.js `"Application error: a client-side exception has occurred"`.
* **Amazon's Resilient Progressive Enhancement:** Amazon does not rely on a single root React component to remain mounted. If its JS scripts encounter origin blocks or fail to execute, they fail silently in the console. The static server-rendered HTML remains fully rendered, functional, and visible.

This proves that BuildingWorld's template architecture is extremely fragile. Crawlers and AI bots that execute JavaScript partially or incorrectly will trigger hydration exceptions, leaving them with a blank screen rather than a crawlable index.

---


## The Hard Fact Truth — Why Following Big Brands Isn't the Right Choice

> [!IMPORTANT]
> The client's justification of poor URL structures and heavy JavaScript dependencies by pointing to Amazon, Flipkart, or IndiaMart is a **fundamental strategy error**. 
> 
> * **The Domain Authority (DR) Gap:** Amazon operates on a **Domain Rating of 96+** with billions of backlinks accumulated over 25+ years. Amazon can rank a blank page or a broken HTML structure because its domain authority is an unstoppable ranking force.
> * **Flawless Technical Execution for Growth:** BuildingWorld.ai has a **Domain Rating of 10**. Startups with low domain authority must be **technically flawless** to compete. If search crawlers or AI agents encounter rendering bailouts, query parameter duplication, raw "undefined" headers, or dynamic hydration crashes, they will simply drop the pages from the index or citations and crawl competitors.
> * **The Reality of Amazon's Routing:** Amazon *never* serves multiple category levels on the same URL path slug using query parameters. Each L1, L2, and L3 category has a unique slug mapping to clean catalog structures.
> 
> Trying to copy the legacy shortcuts of legacy tech giants while operating at DR 10 is an SEO death sentence. To scale from India to global markets, BuildingWorld must execute with **110% technical perfection**.

## The Fix Architecture

The correct fix for a Next.js marketplace is **Server Components with streaming** for the product grid:

```tsx
// app/products/categories/[slug]/page.tsx
// Make the product grid a Server Component with actual DB data

export default async function CategoryPage({ params }) {
  // Fetch first page of products server-side
  const products = await getProductsByCategory(params.slug, { limit: 24 });
  const category = await getCategoryBySlug(params.slug);
  
  return (
    <main>
      {/* H1 in Server Component = in Raw HTML */}
      <h1>{category.name}</h1>
      
      {/* Product grid rendered server-side = in Raw HTML */}
      <section aria-label={`${category.name} products`}>
        {products.map(product => (
          <article key={product.id}>
            <a href={`/products/${product.slug}`}>
              <img
                src={`https://d65kpn1g6p18g.cloudfront.net/images/products/${product.imageKey}`}
                alt={product.name}
                width={300} height={300}
              />
              <h2>{product.name}</h2>  {/* Product name as H2 under category H1 */}
              <p>₹{product.price.toLocaleString('en-IN')}</p>
            </a>
          </article>
        ))}
      </section>
      
      {/* Dynamic filters via Suspense — still SSR but deferred */}
      <Suspense fallback={<FilterSkeleton />}>
        <CategoryFilters categoryId={category.id} />
      </Suspense>
    </main>
  );
}
```

---

*Signed and Certified by:*  
**Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO**  
*Audited & Certified via the Visibility-to-Demand System (VDS) Framework · [seobiopse.com](https://seobiopse.com)*  
*Raw vs Rendered analysis | 15 June 2026*
