# BuildingWorld.ai — Technical SEO / AEO / GEO Audit Deliverables

This repository contains the complete set of audit reports for the Next.js marketplace **BuildingWorld.ai**. The audit focuses on identifying issues in core page templates that prevent high search visibility, AI Overview citations (AEO/GEO), and Agentic Browsing capabilities.

---

## 📂 Deliverables & File Structure

The audit is structured into both human-readable interactive formats and machine-readable markdown files for AI crawlers:

```
├── index.html            # 🌐 Interactive Client Dashboard (HTML/CSS)
├── screenshots/          # 📸 Screenshots illustrating specific issues
├── reports/              # 📋 Markdown reports
│   ├── FULL-AUDIT-REPORT-2026-06-15.md        # Summary of findings & sprint plans
│   ├── RAW-vs-RENDERED-AUDIT-2026-06-15.md    # HTML source vs post-JS rendering comparison
│   ├── amazon_vs_buildingworld_comparison.md  # Amazon routing benchmark
│   └── vds-audit.md                           # VDS audit report & 5-year outlook
├── Amazon/               # 📦 Extracted Amazon source code files used as benchmark
├── raw-source-data/      # 📁 Sitemaps, Raw source logs, and JSON audit files
└── README.md             # 📖 Deliverables overview map
```

Additionally, the following detailed reports are stored in the repository:
* **[amazon_vs_buildingworld_comparison.md](./reports/amazon_vs_buildingworld_comparison.md):** Benchmark comparison challenging the client's comparison of their L2 URL query parameter structure against Amazon's path-based architecture.
* **[FULL-AUDIT-REPORT-2026-06-15.md](./reports/FULL-AUDIT-REPORT-2026-06-15.md):** Template-level summary detailing the 31 specific findings, severity scores, and sprint plans.
* **[RAW-vs-RENDERED-AUDIT-2026-06-15.md](./reports/RAW-vs-RENDERED-AUDIT-2026-06-15.md):** Analysis of client-side JS dependencies showing the gap between raw source HTML and fully-rendered outputs for SEO and AI models.
* **[vds-audit.md](./reports/vds-audit.md):** Executive report covering the 4 pillars of the Visibility-to-Demand System (VDS) and the 5-year strategy matrix.

---

## ⚡ Key Findings Summarized (Amazon vs. BuildingWorld.ai)

1. **The URL Routing Fallacy:** The client's assertion that "Amazon uses query parameters for category levels" is incorrect. Amazon uses unique path-based slugs (e.g., `/Kitchen-Fixtures/b` vs `/Kitchen-Taps/b`) for different levels and only uses parameters for lookups. BuildingWorld's L1/L2 path sharing causes search engines to de-index L2 pages.
2. **Invisible Product Listings:** 94% of images and 100% of product lists on listing pages exist only after JS execution. Non-JS crawlers (including ChatGPT, Perplexity, Claude, and Googlebot wave-1) see an empty box.
3. **Broken Headings:** H1 headers display literal `"undefined"` strings on 5,000+ pages due to server-side hydration values not being resolved before render.
4. **Missing Schemas:** There is no breadcrumb or organization structured data on category and product page templates.
