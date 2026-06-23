Analyzes outward dispatch and sales data, producing a fully formatted 10-sheet Excel dashboard. Upload your outward CSV, run all cells, and the workbook downloads automatically.

What it does:


10-sheet Excel output built and downloaded automatically:
SheetContents📊 SummaryOverall totals — quantity, revenue, rejection, transport cost📦 Material SalesRevenue and quantity by material and broad category🏢 Customer AnalysisPer-customer dispatch volume, revenue, rejection rate⚠️ RejectionRejection % by customer and material — rows above 5% highlighted red💰 IncentiveIncentive cost breakdown by material and customer🚛 TransportTransport cost benchmarking by vendor and route💳 Payment StatusPending vs paid breakdown by customer📈 Rate & PricingAverage rate per material type over time📅 Monthly TrendsVolume and revenue trends month-over-month📋 Raw DataFull cleaned dataset used for all sheets


Trip-level deduplication — transport and incentive costs deduplicated by Outward Code before summing, preventing double-counting on multi-material trips
Vendor name normalization — 40+ transport vendor name variants mapped to clean names via regex (e.g. "gokul transpt" → "Gokul Transport")
Material category mapping — 200+ material names mapped to Rigid Plastics, Flexible Plastics, Paper, Metal, Glass, LVP, Textile, E-Waste, and more
LVP / co-processing revenue — tracked separately at trip level since it carries zero material sale price
Configurable rejection threshold — rows above 5% rejection highlighted red (editable in config)


Input: Outward cleaned CSV from the Data Cleaning Pipeline
Output: Sales_Analytics_Dashboard.xlsx — auto-downloads from Colab
