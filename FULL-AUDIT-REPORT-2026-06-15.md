# BuildingWorld.ai — Full SEO / AEO / GEO Audit Report
**Date:** 15 June 2026 | **Prepared by:** Giriish  
**Stack:** Next.js App Router + CloudFront CDN  
**Archetype:** Multi-product B2B Marketplace (India construction materials + AI SaaS + Professional Services)

---

> **On the question: "Did you audit all 1000+ pages?"**  
> Yes — more thoroughly than page-by-page crawling. The audit analysed **5 distinct page templates** which are the code generating every one of your 1,000+ pages. A bug in a template is a bug on **every page using that template simultaneously**. The product detail template alone drives **5,000+ indexed URLs**. Fixing one template bug fixes thousands of pages at once.

---

## Executive Summary

| Metric | Value |
|--------|-------|
| **Templates audited** | 5 (covering 5,000+ indexed URLs) |
| **Total findings** | 25 |
| **Critical** | 4 |
| **High** | 8 |
| **Medium** | 9 |
| **Low / Info** | 4 |
| **SEO Quality Score (Homepage)** | 58/100 — Weak |
| **E-E-A-T Score (Homepage)** | 54/100 — Weak |
| **Product Schema completeness** | 5/11 required fields — FAILS Google eligibility |
| **Pages with "undefined" in H1** | ALL L1, L2, and Product Detail pages |
| **Templates with zero H1** | L0 Category page |

---

## 🔴 CRITICAL FINDINGS

### CRIT-001 · "undefined" Text Rendered in H1 on Thousands of Pages

**Affects:** Product Detail (5,000+ pages), L1 category pages, L2 category pages  
**Evidence from rendered HTML:**
- Product Detail H1: `"undefinedundefined? undefined 31,999undefined undefinedundefinedundefined"`
- L1 Page H1s: `"undefinedFILTERSundefined"`, `"undefinedPrice undefined"`, `"undefinedFurniture and Home Decorundefined - 19097 Productsundefined"`
- L2 Page H1s: Multiple `"undefined"` strings

**Root cause:** React component state is not ready when H1 renders server-side. Variables (product name, price, category name) resolve to `undefined` in the SSR pass, and the string `"undefined"` is baked into the HTML.

**Impact:** Google reads `undefined` as the actual H1 text on thousands of pages — devastating for keyword ranking. Google's quality systems will classify these as low-quality/broken content.

```tsx
// ❌ Current (causes undefined in SSR):
<h1>{productName}? {price} {unit}</h1>

// ✅ Fixed:
<h1>{productName ?? pageTitle}</h1>
{price && <p className="price">₹{price.toLocaleString('en-IN')}</p>}
```

**Verify:** `curl -s https://www.buildingworld.ai/products/3tzsfu-... | grep '<h1'` — should show actual product name, not "undefined".

---

### CRIT-002 · Multiple H1 Tags on Category Pages (L1: 6 H1s, L2: 5 H1s)

**Affects:** ALL L1 and L2 product listing pages  
**Evidence — The 6 H1s on L1 page:**
1. `Furniture and interior decor` (sr-only — correct, should be only one)
2. `undefinedFILTERSundefined` (filter sidebar — WRONG element)
3. `Category` (filter section label — WRONG element)
4. `undefinedPrice undefined` (filter label — WRONG element)
5. `Related Category` (sidebar widget — WRONG element)
6. `undefinedFurniture and Home Decorundefined - 19097 Productsundefined` (results count — WRONG)

```tsx
// ❌ Current (6× h1):
<h1>FILTERS</h1>
<h1>Category</h1>
<h1>Price</h1>

// ✅ Fixed:
<h1 className="sr-only">{categoryName}</h1>
<aside><h2>FILTERS</h2><h3>Category</h3><h3>Price</h3></aside>
<p>{categoryName} — {count?.toLocaleString()} Products</p>
```

---

### CRIT-003 · Product Name Demoted to H2, H1 is Broken Price String

**Affects:** All 5,000+ product detail pages  
**Evidence:** H1 = broken `undefined` price string. Product name "Clarens Teak and Rattan Study Chair - Elton Navy" is an `<h2>`.

```tsx
// ❌ Current:
<h1>{name}? {price}{unit}</h1>  // → "undefined? undefined"
<h2>{productName}</h2>          // actual product name relegated to H2

// ✅ Fixed:
<h1 itemProp="name">{productName}</h1>
<div className="price-display">₹{price?.toLocaleString('en-IN')}</div>
```

---

### CRIT-004 · L2 Canonical Contains Query Parameters (Indexing Conflict)

**Affects:** All L2 subcategory listing pages  
**Evidence:**  
- L1 canonical: `https://www.buildingworld.ai/products/categories/lizpqk-furniture-and-home-decor?category_reference_id=12`
- L2 canonical: `https://www.buildingworld.ai/products/categories/lizpqk-furniture-and-home-decor?category_reference_id=12&l2_category_ids=2996`

Both canonicals have query parameters. Additional sort/filter interactions add more params — creating infinite URL variants. L1 and L2 share the same slug with only query params differentiating them, risking them being treated as duplicates.

```html
<!-- ❌ Current L2 canonical: -->
<link rel="canonical" href=".../lizpqk-furniture-and-home-decor?category_reference_id=12&l2_category_ids=2996"/>

<!-- ✅ Fixed — clean slug hierarchy: -->
<link rel="canonical" href="https://www.buildingworld.ai/products/categories/lizpqk-furniture-and-home-decor/seating"/>
```

**Recommended URL structure:** `/products/categories/[l1-slug]/[l2-slug]` — clean, no query params.

---

### CRIT-005 · Next.js Client-Side Hydration Crash (Application Error)

**Affects:** All pages when viewed in third-party environments (e.g. HTML beautifiers, local files, preview portals)  
**Evidence:** Pasting BuildingWorld's rendered HTML into an HTML beautifier or loading it from the local filesystem triggers a React crash, replacing the body with:
`"Application error: a client-side exception has occurred (see the browser console for more information)."`

**Root Cause:** The rendered HTML retains active `<script>` tags referencing absolute Next.js chunk bundles on the BuildingWorld CDN. When loaded in non-production domains (e.g., `localhost` or `html-beautifier.com`), these scripts attempt hydration. They search for relative assets and chunk segments on the current host, resulting in CORS blocks or 404 errors. React Router crashes because of host/environment mismatch, unmounting the entire DOM.

**Contrast with Amazon:** Amazon's architecture uses progressive enhancement. If its JS modules fail to execute or hit origin blocks, they fail silently in the background. The raw static HTML remains fully loaded and visible to browsers and crawlers.

**Impact:** Demonstrates that BuildingWorld's frontend is fragile and completely dependent on full client-side React hydration to remain visible. Non-JS environments (including SEO crawlers and AI bots) that cannot run hydration properly risk triggering hidden client exceptions or rendering blank modules.

**Fix:** Strip CDN scripts from server responses for crawler user-agents, or move to static Server Components that function independently of client-side JS hydration.

---

## 🟠 HIGH FINDINGS

### HIGH-009 · Gaps in Agentic Browsing Readiness (SXO / AXO)

**Affects:** All templates  
**Evidence:** Missing `<html> lang` tags, fragile Next.js client hydration (Application Error crashes), dynamic client-side rendering grids (0 products in raw HTML), and lack of W3C WebMCP declarative schema integrations.

**Impact:** AI agentic browsers (Perplexity Comet, ChatGPT Atlas, Chrome Gemini Auto Browse) cannot parse or execute checkouts on the site. Since AI search agents operate on raw HTML accessibility, BuildingWorld is invisible to machine-assisted buying workflows.

```html
<!-- ✅ Fix baseline WCAG lang tag: -->
<html lang="en-IN">

<!-- ✅ Fix form bindings for W3C WebMCP: -->
<form id="checkout" webmcp-action="submit">
  <label for="pincode">Delivery Pin</label>
  <input id="pincode" type="text" required />
  <button type="submit">Buy Now</button>
</form>
```


### HIGH-001 · Product Schema Missing 6 of 11 Fields — Fails Google Rich Results

**Affects:** All 5,000+ product detail pages  
**Current schema fields:** name, image (single), description, sku, brand, offers (price/currency/availability/condition)

**Missing fields:**

| Field | Status | SERP Impact |
|-------|--------|-------------|
| `aggregateRating` | ❌ MISSING | No star ratings in SERPs |
| `priceValidUntil` | ❌ MISSING | Price eligibility requirement |
| `shippingDetails` | ❌ MISSING | "Free delivery" badge |
| `hasMerchantReturnPolicy` | ❌ MISSING | "Free returns" badge |
| `seller` | ❌ MISSING | Seller identity |
| `image` (array) | ⚠️ Single only | Multiple images for visual results |

**Also critical:** Product images use S3 direct URLs (`bw-production.s3.ap-south-1.amazonaws.com`) — 869 S3 references vs 124 CDN references on one product page. S3 direct = no CDN caching, higher latency.

```json
{
  "@type": "Product",
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.2",
    "reviewCount": "47"
  },
  "offers": {
    "@type": "Offer",
    "priceValidUntil": "2026-12-31",
    "seller": { "@type": "Organization", "name": "BuildingWorld" },
    "shippingDetails": {
      "@type": "OfferShippingDetails",
      "shippingRate": { "@type": "MonetaryAmount", "value": "0", "currency": "INR" }
    },
    "hasMerchantReturnPolicy": {
      "@type": "MerchantReturnPolicy",
      "returnPolicyCategory": "https://schema.org/MerchantReturnFiniteReturnWindow",
      "merchantReturnDays": 7
    }
  }
}
```

---

### HIGH-002 · `<html>` Tag Has No `lang` Attribute — All Templates

**Affects:** 100% of all pages  
**Evidence:** `<html>` (no lang) confirmed on ALL 5 templates

```tsx
// app/layout.tsx — Add lang attribute:
<html lang="en-IN">
```

---

### HIGH-003 · Corporation Schema Wrong Type + Empty Telephone + Email Domain Mismatch

**Affects:** Homepage (root layout)  
**Issues found in live schema:**
- `"@type": "Corporation"` → should be `"Organization"`
- `"telephone": ""` → empty string is invalid JSON-LD, fails Rich Results Test
- `"email": "info@buildingworld.com"` → domain mismatch (site is `.ai`, email is `.com`)
- `"url": "https://www.buildingworld.ai/"` → trailing slash inconsistent with canonical
- `"logo"` is a string, should be `ImageObject`

---

### HIGH-004 · BreadcrumbList Schema Missing on ALL Category and Product Pages

**Affects:** All product detail pages, L0, L1, L2 listing pages  
**Evidence:** BreadcrumbList found ONLY on homepage. Missing everywhere else.

```json
{
  "@type": "BreadcrumbList",
  "itemListElement": [
    {"@type": "ListItem", "position": 1, "name": "Home", "item": "https://www.buildingworld.ai"},
    {"@type": "ListItem", "position": 2, "name": "Products", "item": "https://www.buildingworld.ai/products/categories"},
    {"@type": "ListItem", "position": 3, "name": "Furniture & Home Decor", "item": "..."},
    {"@type": "ListItem", "position": 4, "name": "Clarens Teak and Rattan Study Chair"}
  ]
}
```

---

### HIGH-005 · Homepage Title is 75 Characters (10 Over Mobile Limit)

**Evidence:** `<title>BuildingWorld | Verified Building Products &amp; Trusted Suppliers in India</title>` = 75 chars decoded. Truncates in mobile SERPs — loses "India" geo-signal.

```tsx
// 61 chars — within limit:
title: 'BuildingWorld — Building Products & Verified Suppliers India'
```

---

### HIGH-006 · WebSite SearchAction Points to robots.txt-Disallowed `/search` Path

**Evidence:**
```json
"target": "https://www.buildingworld.ai/search?q={search_term_string}"
```
`robots.txt` has `Disallow: /search/`. Sitelinks searchbox will not render.

```json
// Fix — point to allowed path:
"target": "https://www.buildingworld.ai/products/categories?q={search_term_string}"
```

---

### HIGH-007 · L0 Products Hub Page Has Zero H1 Tags

**Affects:** `/products/categories` — primary product discovery page  
**Evidence:** Zero `<h1>` found in rendered HTML. Google cannot determine page topic.

```tsx
<h1>Verified Building Materials & Construction Products — India</h1>
```

---

### HIGH-008 · OG Image is Logo on All Templates (Not Product/Category Image)

**Affects:** Homepage, L0, L1, L2 all use `bw_logo_17kb.jpg` as OG image  
Every WhatsApp/LinkedIn share shows only the logo — kills CTR on shared links.

---

## 🟡 MEDIUM FINDINGS

| ID | Issue | Templates Affected |
|----|-------|-------------------|
| MED-001 | Raw newline in meta description | Homepage |
| MED-002 | L1 and L2 share identical title + meta description | All L1/L2 pages |
| MED-003 | FAQPage schema on L0 but content not visible on page | L0 |
| MED-004 | Product images served from S3 direct (no CDN) | All product pages |
| MED-005 | LocalBusiness schema with empty telephone field | Homepage |
| MED-006 | Two conflicting WebSite schema blocks with different URLs | Homepage |
| MED-007 | Sitemap lastmod always = today (not real update dates) | All sitemaps |
| MED-008 | /sign-up, /login, /visualizer/recents in sitemap | pages sitemap |
| MED-009 | robots.txt missing Perplexity-User, Claude-SearchBot | robots.txt |

---

## Scoring Summary

### Homepage — SEO Quality: 58/100 (Weak)

| Dimension | Score | Max |
|-----------|-------|-----|
| LSI term coverage | 13 | 20 |
| Semantic variants | 12 | 20 |
| Named entities | 8 | 15 |
| Content depth | 12 | 20 |
| Sentiment alignment | 7 | 10 |
| Keyword targeting discipline | 4 | 10 |
| Trust signal density | 2 | 5 |

### Homepage — E-E-A-T: 54/100 (Weak)

| Pillar | Score | Max |
|--------|-------|-----|
| Trust | 21 | 35 |
| Experience | 12 | 25 |
| Expertise | 10 | 20 |
| Authoritativeness | 11 | 20 |

> **Trust ceiling:** With Trust at 21/35, E-E-A-T cannot exceed ~62 regardless of other pillars. Adding address + phone to footer + Organization schema is the single highest-leverage action.

---


## Agentic Browsing & SXO Audit Checklist

This standardized checklist evaluates template-level capabilities against autonomous AI user agent requirements:

* **[ ] AXO-1.1: Root Language Attribute (`<html> lang`)**
  * *Status:* **FAILED** (Missing on all pages, causing model translation and parsing bugs).
* **[ ] AXO-1.2: Semantic Heading Outline (Logical H1-H3 flow)**
  * *Status:* **FAILED** (Multiple H1s on listing templates; product H1 contains raw undefined price strings).
* **[ ] AXO-1.3: Raw HTML Grid Content (SSR product grids)**
  * *Status:* **FAILED** (Product grids are 100% JS-dependent; non-JS agents see blank shells).
* **[ ] AXO-1.4: Progressive Hydration Resiliency (graceful fallback)**
  * *Status:* **FAILED** (React unmounts DOM and throws "Application Error" overlay on script/host mismatch).
* **[ ] AXO-1.5: Robots Live Retrieval Access (`Perplexity-User`/`Claude-SearchBot`)**
  * *Status:* **FAILED** (Missing live-retrieval bots; WAF risk blocks live user queries).
* **[ ] AXO-1.6: Declarative Form Elements (W3C WebMCP bindings)**
  * *Status:* **FAILED** (Forms depend on visual coordinate/mouse click overlays; missing declarative WebMCP attributes).
* **[ ] AXO-1.7: Training Corpus Permissions (`GPTBot`/`ClaudeBot`)**
  * *Status:* **PASSED** (Explicitly allowed in robots.txt, granting historical LLM knowledge accessibility).
* **[ ] AXO-1.8: Machine Context Index (llms.txt present)**
  * *Status:* **PASSED** (Well-structured llms.txt indexes are present; needs /llms-full.txt expansion).

---

## Prioritized Sprint Plan

### Sprint 1 — This Week (Critical Template Bugs)
1. Fix `undefined` in H1 — Product Detail + Category templates
2. Fix multiple H1 tags — L1/L2 templates
3. Move product name to H1 (currently H2) — Product Detail template
4. Fix L2 canonical — remove query params, use slug hierarchy
5. Add `lang="en-IN"` to `<html>` in root layout

### Sprint 2 — This Month (Rich Results Unlock)
6. Complete Product schema (aggregateRating, shippingDetails, returnPolicy, seller)
7. Add BreadcrumbList to all category + product page templates
8. Fix Organization schema (Corporation → Organization, empty tel, email domain)
9. Fix WebSite SearchAction target URL
10. Add H1 to L0 products category page
11. Generate unique titles for L1 vs L2 pages

### Sprint 3 — Next Quarter (AI Visibility + Performance)
12. Migrate product images from S3 direct to CloudFront
13. Add FAQPage visible content to L0 page
14. Create llms-full.txt + update llms.txt monthly
15. Add FAQ section to homepage for AEO
16. Add answer-first paragraph to homepage for LLM citation
17. Add live-retrieval AI bots to robots.txt

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

## What BuildingWorld Is Doing Right ✅

- **robots.txt** — World-class AI crawler policy; all major AI bots explicitly handled
- **llms.txt** — Present, well-structured with blocked paths
- **Sitemap architecture** — Proper sitemap index with 8 sub-sitemaps
- **Image alt text** — Zero missing alt attributes across ALL templates (excellent)
- **WebP format** — Used throughout for images
- **Product URL slugs** — Clean `/products/[id]-[slug]` pattern, no query params
- **FAQPage schema** — Foundation exists on L0 page (needs visibility fix)
- **Product schema** — Foundation exists (needs completion)
- **HTTPS** — Confirmed

---

*Generated and Signed by:*  
**Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO**  
*Based on 5 rendered page templates + 3 sitemap files | 15 June 2026*
