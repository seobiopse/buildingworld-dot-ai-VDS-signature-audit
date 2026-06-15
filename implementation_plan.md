# Implementation Plan — Hard Fact Truth, WCAG Agentic Standards, and Report Polishing

This plan outlines the changes to integrate WCAG Agentic Browsing guidelines, embed issue screenshots, add clickable card animations, and sign all reports with the requested credentials.

---

## User Review Required

> [!IMPORTANT]
> - **Signature Formatting:** The report signatures will be updated to use the exact spelling: `Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO` across all Markdown and HTML files.
> - **Screenshot Relocation:** All issue screenshots have been copied from the brain sandbox directory to the workspace relative directory: `audit-report/screenshots/` to ensure relative linkage works in offline client environments.

---

## Proposed Changes

### 1. Advanced SEO Plugin Update

#### [NEW] [wcag-agentic-browsing.md](file:///e:/Personal%20Project/SEO%20Biopse/advanced-seo-plugin-for-claude-code/skills/seo-audit/references/wcag-agentic-browsing.md)
* Create a new reference markdown profile explaining how **WCAG 2.1 / 2.2 accessibility standards** serve as the baseline for **Agentic Browsing Readiness**.
* Focus on:
  - Language attributes (`html lang`) for screen readers and parser models.
  - Heading hierarchy (no duplicate H1s) to allow crawler path mapping.
  - JS-hydration robustness (resiliency when client scripts fail).
  - Alt tags and structured image attributes for visual models.

#### [MODIFY] [SKILL.md](file:///e:/Personal%20Project/SEO%20Biopse/advanced-seo-plugin-for-claude-code/skills/seo-audit/SKILL.md)
* Add a reference mapping link to the new `wcag-agentic-browsing.md` file.

---

### 2. Audit Report Updates (Markdown & HTML)

#### [MODIFY] [FULL-AUDIT-REPORT-2026-06-15.md](file:///C:/Users/giris/.gemini/antigravity-ide/brain/95b34039-f5f4-4838-bc79-2147ea30919b/FULL-AUDIT-REPORT-2026-06-15.md)
* Insert the **"Hard Fact Truth"** section before the conclusion, explicitly framing the domain rating (DR) gap: Amazon ranks despite code bloat because of its DR 96+ brand authority, whereas BuildingWorld.ai (DR 10) must be technically flawless.
* Add a section for WCAG Agentic Browsing checks.
* Update prepared-by and signature block with the requested string: `Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO`.

#### [MODIFY] [RAW-vs-RENDERED-AUDIT-2026-06-15.md](file:///C:/Users/giris/.gemini/antigravity-ide/brain/95b34039-f5f4-4838-bc79-2147ea30919b/RAW-vs-RENDERED-AUDIT-2026-06-15.md)
* Insert **"The Hard Fact Truth"** section.
* Update bottom signature with `Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO`.

#### [MODIFY] [amazon_vs_buildingworld_comparison.md](file:///C:/Users/giris/.gemini/antigravity-ide/brain/95b34039-f5f4-4838-bc79-2147ea30919b/amazon_vs_buildingworld_comparison.md)
* Insert **"The Hard Fact Truth"** section.
* Add WCAG benchmark rows comparing Amazon and BuildingWorld.
* Update bottom signature.

#### [MODIFY] [index.html](file:///e:/Personal%20Project/SEO%20Biopse/Building%20World%20AI/audit-report/index.html)
* **Animations:** Add CSS transitions, hover scales (`scale(1.01)`), shadow glows, and pointer cursor overrides to make the finding cards feel alive and indicate clickability. Add a dynamic dropdown indicator.
* **Screenshots:** Embed relative images `./screenshots/` under the relevant cards:
  - `CRIT-001` (H1 undefined): `product_h1_bug_1781528514945.webp` and `raw_h1_issue_1781529009109.png`
  - `CRIT-003` (CSR Grid): `category_raw_vs_rendered_1781529073420.webp` and `rendered_grid_1781529099622.png`
  - `CRIT-004` (Canonical): `canonical_issue_1781529265348.png`
  - `CRIT-005` (Hydration crash): `view_source_empty_1781529229882.png`
  - `HIGH-001` (Detail page images): `rendered_product_page_1781529044567.png`
  - **Overview (Domain Migration):** Add the redirect loops screens (`step1_redirect_home...` through `step5_dealer_redirect...`) inside a visual flex gallery to show the migration flows.
* **Genuine Strengths actual screenshots:** Add code blocks showing sitemaps and images showing working components (e.g. `media__1781528705099.png`, `media__1781528714834.png`).
* **Signature:** Update prepared-by, sticky progress label, and signature band with: `Giriish | Organic Gorwth Architec | Founders of VDS | Father of SEO`.

---

## Verification Plan

### Automated Verification
* Run standard HTML markup validation checks on `index.html`.
* Ensure that all relative paths `./screenshots/*` exist in the `screenshots` folder.

### Manual Verification
* Deploy/render `index.html` locally and interact with findings cards: check hover effects, check expand/collapse animations, and verify images are sized correctly.
* Verify progress count calculations update correctly when checkboxes are toggled.
