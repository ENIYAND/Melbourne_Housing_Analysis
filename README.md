# 📊 Melbourne Housing Market — Power BI Dashboard

🔗 **Live Dashboard:**  
https://app.powerbi.com/view?r=eyJrIjoiMmI1Y2YzMjctMTQ3Mi00Njg2LTg4MmQtYWExNDQ1YzRiMDkwIiwidCI6ImM2ZTU0OWIzLTVmNDUtNDAzMi1hYWU5LWQ0MjQ0ZGM1YjJjNCJ9

---

# 🌟 Project Overview

This Power BI dashboard analyses **13,580 residential property transactions** across the Melbourne metropolitan area, covering the period **January 2016 to November 2017**.

The dashboard was built as part of a full-stack Data Analyst assessment covering SQL, Python, and Power BI. The Power BI component consumes a pre-cleaned dataset prepared through Python (pandas, scipy) and delivers a **4-page interactive decision-support dashboard** for real estate agencies, investors, and property analysts.

The dashboard enables stakeholders to:

- Track average sale price and transaction volume trends month-over-month
- Identify which suburbs drive 80% of Melbourne's total housing revenue
- Evaluate market-wide KPIs at an executive level
- Analyse price-per-room efficiency by region and property age

---

# 🏢 Business Problem

Melbourne's housing market contains 314 suburbs, 3 property types, 8 metropolitan regions, and 18 months of transaction data. Without a structured analytical layer, stakeholders face:

- No visibility into which suburbs are generating disproportionate revenue
- No way to distinguish seasonal price movement from structural appreciation
- No benchmark for comparing property value across age brackets and regions
- Manual, static Excel reports with no interactivity or drill-through capability

This dashboard provides a **single source of truth** for Melbourne housing market performance, enabling data-driven decisions on agent territory allocation, pricing strategy, listing timing, and investment targeting.

---

# 💻 Tech Stack

| Layer | Tool |
|---|---|
| Data Preparation | Python (pandas, scipy, missingno) |
| Data Cleaning | Jupyter Notebook → melb_clean.csv |
| Dashboard Development | Power BI Desktop |
| Data Transformation | Power Query (M Language) |
| Business Calculations | DAX (Data Analysis Expressions) |
| Data Model | Star Schema |
| Version Control | GitHub |

---

# 🛠️ Power BI Features Implemented

- **Star schema data model** — fact_properties, dim_suburb, DimDate
- **DAX Calendar Table** with Year, Month, MonthName, YearMonth, Quarter columns
- **Time intelligence measures** — DATEADD, DATESINPERIOD for MoM calculations
- **3-Month Rolling Average** using DATESINPERIOD for trend smoothing
- **Dynamic Pareto DAX measure** — recalculates cumulative revenue % with every filter change
- **Conditional formatting** — green/red on MoM change KPI cards
- **Drill-through pages** — right-click any suburb to navigate to suburb-level detail
- **Bookmarks and navigation buttons** — sidebar navigation across all 4 pages
- **Custom tooltips** — enabled on data points only, disabled on static elements
- **Combo chart** — dual-axis line and column for Pareto distribution
- **Azure Maps scatter visual** — geographic price quartile concentration map
- **Page-level filters** — Method filter on Executive page (completed transactions only)

---

# 📂 Dataset Overview

**Source:** Melbourne Housing Dataset (assessment_melb.xlsx)  
**Period:** January 2016 — November 2017  
**Geography:** Melbourne Metropolitan Area + Regional Victoria  
**Records:** 13,580 property transactions across 314 suburbs

## Data Loaded into Power BI

### fact_properties (melb_clean.csv)
Property-level transaction data after Python cleaning.

| Column | Description |
|---|---|
| Suburb | Suburb of property |
| Address | Street address |
| Rooms | Number of rooms |
| Type | h = House, u = Unit, t = Townhouse |
| Price | Sale price in AUD |
| Method | S = Sold, SP = Sold Prior, PI = Passed In, VB = Vendor Bid, SA = Sold After |
| SellerG | Real estate agent / agency |
| Date | Date of sale |
| Distance | Distance from Melbourne CBD (km) |
| Regionname | Metropolitan region classification |
| BuildingArea | Building area in sqm (null-treated) |
| YearBuilt | Year property was built (null-treated) |
| price_per_room | Price ÷ Rooms (engineered feature) |
| price_per_landsize | Price ÷ Landsize (engineered feature) |
| property_age | 2017 − YearBuilt (engineered feature) |
| age_bracket | Binned age category (Historic / Post-War / Mid-Century / Modern) |
| price_to_area_ratio | Suburb avg price ÷ avg building area |

### dim_suburb
Suburb-level aggregated metrics (314 rows, one per suburb).

| Column | Description |
|---|---|
| Suburb | Suburb name |
| CouncilArea | Council Area name |
| Region | Region name |

### DimDate (DAX-generated)
Calendar table created in Power BI. Covers full date range of the dataset.

| Column | Description |
|---|---|
| Date | Full date |
| Year | Calendar year |
| Month | Month number |
| MonthName | Abbreviated month (Jan, Feb...) |
| YearMonth | MMM YYYY format for axis labels |
| Quarter | Q1–Q4 |

> Note: Date-derived columns (Sale_Year, Sale_Month, Sale_MonthName, Sale_YearMonth) computed in Python were intentionally dropped during Power Query loading. All date dimensions are sourced exclusively from DimDate to enable Power BI time intelligence functions.

---

# 🗂️ Data Model

The Power BI model follows a **Star Schema**:

```
DimDate ──────────────────────────────────┐
  [Date]                                  │ (Many-to-One, Active)
                                          ▼
                               fact_properties [Date]
                               fact_properties [Suburb]
                                          │ (Many-to-One, Active)
                                          ▼
                                    dim_suburb [Suburb]
```

> All DAX measures are stored in a dedicated `_Measures` table, not inside fact tables, following Power BI model hygiene best practices.

---

# 💡 Dashboard Overview — 4 Pages

---
<img width="1150" height="629" alt="image" src="https://github.com/user-attachments/assets/072ab817-c930-4e7c-a537-d9ec67a9d414" />

## 🏠 Page 1 — Executive View

**Business Question:** What is the current state of the Melbourne housing market?

**KPI Cards (5):**
- Total Revenue — AUD 14.26bn
- Properties Sold — 11K (completed transactions: S, SP, SA only — PI and VB excluded)
- Avg Sale Price — AUD 1.07M
- MoM Price Change — current vs prior month with conditional formatting
- Avg Distance from CBD — 10.16km

**Visuals:**
- Avg Sale Price by Property Type (Bar Chart)
- Total Revenue by Top 10 Suburbs (Bar Chart)
- Properties Sold by Type — House / Unit / Townhouse (Donut Chart)

**Slicers:** Year · CouncilArea · Region · Suburb

---
<img width="1148" height="628" alt="MoM Trend analysis" src="https://github.com/user-attachments/assets/32c7fe98-8bd7-419e-b733-68c67babbb25" />

## 📈 Page 2 — MoM Trend Analysis

**Business Question:** Is the Melbourne market appreciating or declining, and is volume following the same pattern?

**KPI Cards (4):**
- Avg Sale Price
- MoM Price Change % (conditional: green positive / red negative)
- Total Transactions
- MoM Volume Change % (conditional formatting)

**Visuals:**
- Market Momentum: Sales Volume vs Price Trends — dual-axis combo chart with 3-month rolling average overlay
- MoM Price Change % by Month Year — waterfall-style bar chart with positive/negative conditional formatting

**Slicers:** Year · CouncilArea · Region · Suburb

---
<img width="1145" height="622" alt="image" src="https://github.com/user-attachments/assets/535ce796-98e6-4941-9bd4-de98b7cd2140" />

## 📊 Page 3 — Pareto Analysis

**Business Question:** Which suburbs drive 80% of Melbourne's total housing revenue?

**KPI Cards (3):**
- Total Revenue (All Suburbs) — AUD 14.26bn
- Suburbs Driving 80% Revenue — 92
- Revenue Concentration Ratio — 29.30%

**Visuals:**
- Suburb Revenue Concentration Pareto Chart — combo chart (bars = suburb revenue, line = cumulative %, reference line at 80%)
- Top 20 Revenue Suburbs Table — with revenue data bars, avg sale price, and sales volume

**Slicers:** Year · CouncilArea · Region · Suburb

---
<img width="1147" height="627" alt="Strategic_insights" src="https://github.com/user-attachments/assets/3f0e8fd8-3de9-4156-8209-ee77f35842c3" />

## 🗺️ Page 4 — Strategic Insights Analysis

**Business Question:** Where is premium value concentrated geographically, and which regions and property age brackets command the highest price efficiency?

**Visuals:**
- Geographic Price Concentration Heatmap — Azure Maps scatter plot with Q1–Q4 price quartile colour coding
- Price Per Room by Region — horizontal bar chart across 8 regions
- Price Per Room by Age Bracket — column chart across Historic / Post-War / Mid-Century / Modern

**Slicers:** Year · CouncilArea · Region · Suburb

---

# 💡 Key Insights & Recommendations

All insights follow the DA framework: **Finding → Decision → Who Acts → Risk**

---

## Page 2 — MoM Trend Analysis

### Insight 1 — Price and Volume Moving in Opposite Directions
**Finding:** MoM Price Change at -2.74% while MoM Volume Change is +10.27%. Sep-2017 had 902 transactions vs Aug-2017's 819 — more sellers, lower prices.  
**Decision:** Sellers should list within 30 days. Rising volume with falling price is an early softening signal.  
**Who Acts:** Seller-side agents — reset client price expectations by ~2.7% from Aug-2017 valuations immediately.  
**Risk:** Could be seasonal spring listings temporarily inflating volume. Review Oct-2017 data to confirm trend vs noise.

### Insight 2 — Jul-2017 Volume Spike Crashed Price, Not Lifted It
**Finding:** Jul-2017 — highest volume in entire dataset (1,512 transactions) — yet MoM price change was -18.34%, the largest single-month drop.  
**Decision:** High transaction volume is not a proxy for price strength. Any model using volume as a bullish price signal would have been wrong by 18% in Jul-2017.  
**Who Acts:** Analysts — never present a volume spike as positive without checking concurrent price direction.  
**Risk:** None — historical calibration finding.

### Insight 3 — Aug-2017 Price Spike Is a Statistical Outlier
**Finding:** Aug-2017 shows +34.59% MoM price jump to AUD 1.19M. Volume simultaneously dropped from 1,512 to 819. The 3M rolling average sits materially below this spike — confirming distortion by a small cluster of high-value transactions.  
**Decision:** Do not use AUD 1.19M Aug-2017 average as a pricing benchmark. Use the 3M rolling average as the reference.  
**Who Acts:** All pricing decision-makers — any client quoted Aug-2017 average is currently overpriced by ~AUD 30K–50K.  
**Risk:** Clients anchored to the Aug-2017 peak will resist price reductions — manage expectations proactively.

### Insight 4 — 18-Month Trend Is Genuine Appreciation
**Finding:** Price moved from AUD 0.95M (Mar-2016) to AUD 1.16M (Sep-2017) — a sustained +22% gain confirmed by the 3M rolling average across both years.  
**Decision:** Long-hold investors who entered in early 2016 hold strong unrealised gains. Oct-Nov 2017 data will confirm continuation or plateau.  
**Who Acts:** Investment advisory teams — flag the 22% gain in client portfolio reviews as a realised exit opportunity.  
**Risk:** Sep-2017 MoM decline of -2.74% is the first warning signal of possible trend reversal.

---

## Page 3 — Pareto Analysis

### Insight 1 — 29.3% of Suburbs Drive 80% of Revenue
**Finding:** 92 suburbs out of 314 generate 80% of Melbourne's AUD 14.26bn total revenue. The remaining 222 suburbs share the last 20%.  
**Decision:** Equal resource allocation across 314 suburbs wastes 70% of effort. Focused coverage of 92 suburbs captures the same revenue outcome.  
**Who Acts:** Agency heads — restructure agent territory assignments to prioritise Pareto suburbs.  
**Risk:** Over-concentration in 92 suburbs increases exposure if these areas soften simultaneously — Sep-2017 data already shows this beginning.

### Insight 2 — Brighton Leads Revenue But Not Volume
**Finding:** Brighton is #1 in total revenue (AUD 34.79Cr) with only 186 transactions. Avg price AUD 18,70,824 — highest in top 20.  
**Decision:** Brighton is a low-volume, high-value market requiring specialist luxury agents, longer sales cycles, and premium marketing budgets — not volume-focused agents.  
**Who Acts:** Agency directors — assign specialist agents to Brighton specifically.  
**Risk:** 186 annual transactions means one slow quarter significantly impacts dependent agent revenue.

### Insight 3 — Reservoir is Volume Engine, Not Margin Leader
**Finding:** Reservoir has 356 transactions (highest in top 20) but ranks #7 in revenue at AUD 24.65Cr with avg price AUD 6,92,646 — lowest in top 20. Brighton's average is 2.7× higher on 170 fewer transactions.  
**Decision:** Brighton generates more commission revenue per transaction than Reservoir. Agencies maximising margin per effort should deprioritise Reservoir volume.  
**Who Acts:** Commission-structure teams — calculate actual commission yield per suburb before territory assignment.  
**Risk:** Volume agents in Reservoir have higher income stability through downturns — lower margin, more consistent.

### Insight 4 — Camberwell and Balwyn Are Underleveraged High-Value Suburbs
**Finding:** Camberwell — 131 transactions, avg AUD 17,18,467 (3rd highest). Balwyn — 106 transactions (lowest in top 20), avg AUD 18,33,905 (2nd only to Brighton).  
**Decision:** Low transaction count with high average price signals low agent competition. 10 additional transactions in each suburb = ~AUD 3.5Cr GMV.  
**Who Acts:** Business development managers — target Camberwell and Balwyn for new agent recruitment immediately.  
**Risk:** Small market size may require undercutting established local agencies on commission rates to enter.

---

## Page 1 — Executive View

### Insight 1 — Market Is Structurally House-Driven
**Finding:** Houses = 70.64% of transactions (8K of 11K). Avg house price AUD 1.2M = 2× unit avg (AUD 0.6M) and 33% above townhouses (AUD 0.9M).  
**Decision:** Any agency with less than 65% house listings is underrepresenting the dominant market segment.  
**Who Acts:** Agency principals — audit current listing mix against the 70/21/8 market split.  
**Risk:** Market shift toward units (affordability-driven) would expose agencies over-indexed on houses.

### Insight 2 — Market Currently Below Its Own Peak Benchmark
**Finding:** Current price AUD 1.16M against Aug-2017 goal of AUD 1.19M — a -2.74% shortfall. Most recent data point is below peak.  
**Decision:** Reset all pending property appraisals to AUD 1.16M baseline — AUD 1.19M is not the current market.  
**Who Acts:** Valuation teams — update all active appraisals this week.  
**Risk:** If -2.74% continues for two more months, 3M rolling average will visibly turn downward, triggering broader sentiment shift.

### Insight 3 — Top Revenue Suburbs Are Two Different Markets
**Finding:** Top 10 revenue suburbs include inner-ring high-value suburbs (Brighton, Glen Iris, Kew, Camberwell) and mid-ring high-volume suburbs (Bentleigh East at 13.88km, Reservoir at 11.42km). One top-10 list contains two structurally different market types.  
**Decision:** Segment the top 10 into inner-ring and mid-ring tiers. Apply different agent profiles, marketing spend, and pricing strategies to each group.  
**Who Acts:** Agency strategy teams — create two separate suburb tiers for resource allocation.  
**Risk:** One-size-fits-all approach to the top 10 will underperform in both segments simultaneously.

---

## Page 4 — Strategic Insights Analysis

### Insight 1 — Southern Metropolitan Commands the Highest Price Per Room
**Finding:** Southern Metropolitan = AUD 0.47M per room — highest of 8 regions and 3.9× higher than Western Victoria (AUD 0.12M). Gap vs second-highest Eastern Metropolitan (AUD 0.33M) = AUD 0.14M per room, a 42% premium.  
**Decision:** Buyers seeking value-per-room should target Northern Metropolitan (AUD 0.32M) or Western Metropolitan (AUD 0.29M) — 30–38% lower cost per room than Southern Metro.  
**Who Acts:** Buyer's agents — use price-per-room as the primary cross-region comparison metric.  
**Risk:** Lower price-per-room in western/northern regions may reflect genuine amenity gaps — lower price may carry lower liveability.

### Insight 2 — Historic Properties Carry a Clear Per-Room Premium
**Finding:** Historic (<1917) = AUD 0.51M per room. Post-War (1917–1950) = AUD 0.47M. Modern (2000+) = AUD 0.32M. Mid-Century (1950–2000) = AUD 0.31M. Older = more expensive per room — counterintuitive but consistent.  
**Decision:** Developers acquiring pre-1950 properties for demolition and modern rebuild are destroying AUD 0.19M–0.20M per room in premium value. Conduct cost-benefit analysis before any heritage demolition.  
**Who Acts:** Developers and buyer's agents — quantify the heritage premium in every client advisory.  
**Risk:** Premium is concentrated in inner suburbs. Validate by Region × Age Bracket before applying universally.

### Insight 3 — Modern Properties Are the Worst Value Per Room
**Finding:** Modern (2000+) AUD 0.32M and Mid-Century (1950–2000) AUD 0.31M are virtually identical — both AUD 0.20M below historic stock per room. On a 3-bedroom property, this = AUD 0.60M total price difference.  
**Decision:** A buyer choosing modern vs historic is making a AUD 0.60M valuation decision, not just a style preference. This must be explicitly quantified in buyer advisory conversations.  
**Who Acts:** Buyer's agents — include age bracket price-per-room comparison in every property shortlist.  
**Risk:** Modern properties carry lower maintenance costs and higher energy efficiency — the per-room cost advantage of historic stock has ongoing offset costs.

---

# 🔑 DAX Measures Reference

| Measure | Formula Summary |
|---|---|
| Total Revenue | SUM(Price) |
| Total Transactions | COUNTROWS(fact_properties) |
| Avg Sale Price | DIVIDE(SUM(Price), COUNTROWS) |
| Avg Price Prior Month | CALCULATE(Avg Sale Price, DATEADD(-1, MONTH)) |
| MoM Price Change % | DIVIDE(current - prior, prior) |
| Avg Price 3M Rolling | DATESINPERIOD last 3 months average |
| Suburb Cumulative Revenue % | Dynamic cumulative % — recalculates on filter |
| Is Pareto Suburb | IF(Cumulative % ≤ 80, "Top 80%", "Remaining 20%") |
| Pareto Suburb Count | COUNTROWS where cumulative ≤ 80% |
| House vs Unit Price Gap | (House avg − Unit avg) ÷ Unit avg × 100 |

---

# 🚀 Project Outcome

| Metric | Value |
|---|---|
| Records analysed | 13,580 transactions |
| Suburbs covered | 314 across Melbourne metro and regional Victoria |
| Date range | Jan 2016 — Nov 2017 (22 months) |
| Dashboard pages | 4 (Executive, MoM Trends, Pareto, Strategic Insights) |
| Total insights documented | 15 across all pages |
| DAX measures created | 12 core measures in dedicated _Measures table |
| Data preparation | Python notebook — null treatment, outlier winsorization, feature engineering |

---

# 📁 Repository Structure

```
Melbourne_Housing_Analysis/
├── sql/
│   └── sql_assessment.txt          ← 3 SQL queries with window functions
├── python/
│   ├── Melbourne_Housing_Analysis.ipynb    ← Full EDA and feature engineering notebook
│   ├── melb_clean.csv              ← 13,580 rows × 32 cols (clean dataset)
│   └── melb_suburb_summary.csv     ← 314 suburb-level aggregations
├── powerbi/
│   └── Melbourne_analysis.pbix    ← Power BI dashboard file
└── README.md                       ← This file
```

---

# 📬 Contact

**LinkedIn:** www.linkedin.com/in/tamil-eniyan-a7116a171/
**Email:** umayalini64@gmail.com
