# 📤 Outward / Sales & Dispatch Analytics

Analyzes outward material dispatch and sales data — producing a fully formatted 10-sheet Excel dashboard covering material revenue, customer rejection, transport cost benchmarking, incentive tracking, payment status, pricing trends, and monthly trends. Upload a CSV, run all cells, download the dashboard.


## The Problem It Solves

Outward dispatch records have one row per material per trip. A single dispatch with 4 materials has 4 rows — but the transport cost, driver details, and vehicle are the same across all 4. Summing costs directly across rows causes **4x overcount**. Identifying which customers reject the most material, which transport vendors are most cost-efficient, or which materials generate the best revenue required manually building separate pivot tables for each question.

This notebook handles deduplication automatically and builds all 10 analysis sheets in **under 60 seconds**.

---

## Architecture

```
Cleaned Outward CSV Upload
          │
          ▼
┌──────────────────────────────┐
│  1. Data Load & Type Cast    │  ← numeric coercion, date parsing
└─────────────┬────────────────┘
              │
              ▼
┌──────────────────────────────┐
│  2. Transport Vendor         │  ← 40+ name variants → clean names (regex)
│     Normalization            │
└─────────────┬────────────────┘
              │
              ▼
┌──────────────────────────────┐
│  3. Material Category        │  ← 200+ names → Rigid Plastics, Paper, etc.
│     Mapping (BROAD_CAT)      │
└─────────────┬────────────────┘
              │
              ▼
┌──────────────────────────────┐
│  4. Trip-Level Deduplication │  ← costs deduped by Outward Code before sum
└─────────────┬────────────────┘
              │
              ▼
┌──────────────────────────────┐
│  5. 10-Sheet Analysis Build  │
│   ├── Summary                │
│   ├── Material Sales         │
│   ├── Customer Analysis      │
│   ├── Rejection              │
│   ├── Incentive              │
│   ├── Transport              │
│   ├── Payment Status         │
│   ├── Rate & Pricing         │
│   ├── Monthly Trends         │
│   └── Raw Data               │
└─────────────┬────────────────┘
              │
              ▼
┌──────────────────────────────┐
│  6. Excel Formatting         │  ← styled headers, column widths, red flags
└─────────────┬────────────────┘
              │
              ▼
   Sales_Analytics_Dashboard.xlsx
```

---

## Languages & Libraries

| Layer | Tool | Purpose |
|---|---|---|
| Language | Python 3 | Core logic |
| Data processing | Pandas | Groupby, deduplication, pivots |
| Numeric ops | NumPy | Safe coercion, conditional columns |
| Text matching | `re` (regex) | Vendor name normalization |
| Excel output | openpyxl | Sheet build, styles, borders, column widths |
| Environment | Google Colab | Upload / download |

---

## Output — 10 Excel Sheets

| Sheet | What It Shows |
|---|---|
| 📊 **Summary** | Total dispatched quantity, total revenue, total rejection, total transport cost, net material sales |
| 📦 **Material Sales** | Revenue and quantity by individual material and broad category (Rigid Plastics, Paper, Metal, etc.) |
| 🏢 **Customer Analysis** | Per-customer: dispatched qty, accepted qty, revenue, rejection %, avg rate, payment status breakdown |
| ⚠️ **Rejection** | Rejection % per customer and material — rows above 5% highlighted red |
| 💰 **Incentive** | Incentive cost breakdown by material and customer — total incentive vs net sales impact |
| 🚛 **Transport** | Per-vendor: total trips, total cost, cost per kg, cost per trip — benchmarked across all transport vendors |
| 💳 **Payment Status** | Paid vs pending breakdown by customer — outstanding amount and invoice count |
| 📈 **Rate & Pricing** | Average rate per material type, rate variance over time, high vs low rate materials |
| 📅 **Monthly Trends** | Month-over-month: total dispatched quantity, revenue, rejection %, transport cost |
| 📋 **Raw Data** | Full cleaned and enriched dataset used for all sheets |

---

## Features in Detail

### Trip-Level Cost Deduplication
When a single Outward Code has 3 material rows, transport cost appears on all 3 rows. Summing directly triples the cost. The notebook groups by Outward Code and takes `first()` on trip-level cost columns before aggregating — ensuring each trip's cost is counted exactly once.

### Transport Vendor Normalization
40+ transport vendor name variants from real dispatch records mapped to clean names using regex:
- `"gsj..."` / `"gst transport..."` / `"gjs..."` → `"GSJ Logistics"`
- `"gokul..."` → `"Gokul Transport"`
- `"tiru..."` / `"thiru..."` → `"Thiru / Tiru Transport"`

### Material Category Mapping
200+ specific material names mapped to broad categories for roll-up analysis:
- `Plastics_PETE_Bottle_Natural_Grade-1` → `Rigid Plastics`
- `Plastics_LDPE_Grade-1` → `Flexible Plastics`
- `LVP (Co-Processing)` → `LVP` (tracked separately — zero material sale price, trip-level revenue only)

### Rejection Threshold Flagging
Customer-material rows with rejection % above 5% are automatically highlighted red in the Rejection sheet. Threshold is configurable at the top of the notebook (`REJECTION_THRESHOLD_PCT = 5`).

### LVP / Co-Processing Revenue Handling
LVP material has zero per-kg sale price — revenue is reported at trip level separately. The notebook isolates LVP rows and reports them independently to avoid distorting material revenue averages.

---

## Use Cases

- **Monthly sales reporting** — Summary and Material Sales sheets go directly into management reports
- **Customer quality review** — Rejection sheet flags which customers to follow up with about material quality
- **Transport vendor negotiation** — Transport sheet benchmarks cost per kg across all vendors for procurement decisions
- **Outstanding payment follow-up** — Payment Status sheet lists all pending invoices by customer
- **Pricing analysis** — Rate & Pricing sheet shows which materials are being underpriced relative to market
- **Trend monitoring** — Monthly Trends sheet tracks volume and revenue trajectory without manual charting

---

## Time Saved

| Task | Manual (Excel) | This Notebook |
|---|---|---|
| Deduplicate trip costs across materials | 30–45 min | < 5 sec |
| Build customer rejection pivot | 20–30 min | < 5 sec |
| Transport cost benchmarking | 20–30 min | < 5 sec |
| Material revenue by category | 15–20 min | < 5 sec |
| Monthly trends table | 15–20 min | < 5 sec |
| Format & share-ready Excel output | 20–30 min | < 5 sec |
| **Total per reporting cycle** | **~3–4 hours** | **< 60 sec** |

**Estimated time saving: ~98% reduction per reporting cycle.**

---

## Input / Output

| | Detail |
|---|---|
| **Input** | Outward cleaned CSV from the [Data Cleaning Pipeline](../data-cleaning-pipeline/) |
| **Output** | `Sales_Analytics_Dashboard.xlsx` — 10-sheet formatted workbook, auto-downloads |

---

## How to Use

### Google Colab
1. Click **Open in Colab** above
2. **Cell 1** — run once per session (`pip install openpyxl`)
3. **Cell 2** — upload your cleaned outward CSV when prompted
4. **Cell 3** — all 10 sheets are built and the Excel file downloads automatically

---

## Configuration

Edit these values at the top of Cell 3 before running:

```python
OUTPUT_FILENAME         = "Sales_Analytics_Dashboard.xlsx"  # output filename
REJECTION_THRESHOLD_PCT = 5   # rows above this % are highlighted red in Rejection sheet
```

Add new transport vendor mappings to `VENDOR_MAP` as new vendors appear in future exports.

---
