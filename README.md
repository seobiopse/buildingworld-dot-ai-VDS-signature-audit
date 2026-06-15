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
│   └── amazon_vs_buildingworld_comparison.md  # Amazon routing benchmark
├── Amazon/               # 📦 Extracted Amazon source code files used as benchmark
├── raw-source-data/      # 📁 Sitemaps, Raw source logs, and JSON audit files
└── README.md             # 📖 Deliverables overview map
```

Additionally, the following detailed reports are stored in the repository:
* **[amazon_vs_buildingworld_comparison.md](./reports/amazon_vs_buildingworld_comparison.md):** Benchmark comparison challenging the client's comparison of their L2 URL query parameter structure against Amazon's path-based architecture.
* **[FULL-AUDIT-REPORT-2026-06-15.md](./reports/FULL-AUDIT-REPORT-2026-06-15.md):** Template-level summary detailing the 31 specific findings, severity scores, and sprint plans.
* **[RAW-vs-RENDERED-AUDIT-2026-06-15.md](./reports/RAW-vs-RENDERED-AUDIT-2026-06-15.md):** Analysis of client-side JS dependencies showing the gap between raw source HTML and fully-rendered outputs for SEO and AI models.

---

## 🌐 How to Share the Interactive HTML Dashboard

The dashboard [index.html](file:///e:/Personal%20Project/SEO%20Biopse/Building%20World%20AI/index.html) is built with vanilla HTML/CSS and is fully responsive. It functions as a client-shareable dashboard with interactive checklist capability (saves checked progress in localStorage).

### Method 1: Automatic Deployment via GitHub Actions (Recommended)
You can configure a GitHub Action to deploy the dashboard directly to GitHub Pages whenever you push updates.

Create a workflow file in `.github/workflows/deploy-report.yml`:

```yaml
name: Deploy Audit Report to GitHub Pages

on:
  push:
    branches:
      - main
    paths:
      - 'index.html'
      - 'screenshots/**'

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./
```

Once the action runs, the report will be available publicly at `https://<your-username>.github.io/<repository-name>/`.

### Method 2: Manual Sharing
Because `index.html` is a standalone file, you can:
1. Double-click the file to run it locally in any web browser.
2. Zip the repository files (`index.html` and `screenshots` folder) and email it directly to the client.

---

## ⚡ Key Findings Summarized (Amazon vs. BuildingWorld.ai)

1. **The URL Routing Fallacy:** The client's assertion that "Amazon uses query parameters for category levels" is incorrect. Amazon uses unique path-based slugs (e.g., `/Kitchen-Fixtures/b` vs `/Kitchen-Taps/b`) for different levels and only uses parameters for lookups. BuildingWorld's L1/L2 path sharing causes search engines to de-index L2 pages.
2. **Invisible Product Listings:** 94% of images and 100% of product lists on listing pages exist only after JS execution. Non-JS crawlers (including ChatGPT, Perplexity, Claude, and Googlebot wave-1) see an empty box.
3. **Broken Headings:** H1 headers display literal `"undefined"` strings on 5,000+ pages due to server-side hydration values not being resolved before render.
4. **Missing Schemas:** There is no breadcrumb or organization structured data on category and product page templates.
