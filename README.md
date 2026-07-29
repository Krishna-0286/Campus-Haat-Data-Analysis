# Campus Heart Data Analysis

End-to-end data analysis of a campus food delivery platform — covering data cleaning, exploratory analysis, business insight generation, and interactive dashboards (Power BI + Streamlit).

The project analyzes two linked datasets from the platform: **order placement** (`order_campus.csv`) and **delivery fulfillment** (`new_delivery_data.csv`), joined on a shared order/delivery key, to answer a core business question: **where in the order-to-delivery journey does the business lose customers and time, and what should be done about it?**

---

## 📌 Project Overview

- **Domain:** Campus food ordering & delivery platform
- **Scale:** 246,043 orders · 271,598 delivery records (2018–2025)
- **Goal:** Clean the raw data, surface actionable business insights, and present them through both a static report and a live interactive dashboard
- **Deliverables:** Jupyter notebook (full pipeline), Power BI dashboard, Streamlit web app, HTML report, interview-style documentation of data-cleaning methodology

---

## 🗂️ Repository Structure

```
campus-heart-data-analysis/
│
├── data/
│   ├── order_campus.csv                     # Raw order-level data
│   ├── new_delivery_data.csv                # Raw delivery-lifecycle data
│   ├── orders_cleaned_for_powerbi.csv        # Cleaned orders, ready for BI tools
│   ├── delivery_cleaned_for_powerbi.csv      # Cleaned deliveries, ready for BI tools
│   └── seller_summary_for_powerbi.csv        # Pre-aggregated seller reliability table
│
├── notebooks/
│   └── delivery_order_full_analysis.ipynb    # Full cleaning + EDA + insights pipeline
│
├── dashboard/
│   ├── streamlit_dashboard.py                # Interactive Streamlit web app
│   └── business_analysis_dashboard.html      # Standalone static report (no server needed)
│
├── docs/
│   ├── data_cleaning_error_types_interview_guide.md
│   └── powerbi_dashboard_guide.md            # Step-by-step Power BI build guide
│
└── README.md
```

*(Rename/organize folders to match your actual repo layout before pushing — adjust paths above if your files sit flat in the repo root.)*

---

## 🧰 Tech Stack

| Purpose | Tools |
|---|---|
| Data cleaning & analysis | Python, Pandas, NumPy |
| Visualization (static/report) | Matplotlib, Plotly |
| Interactive dashboard | Streamlit |
| Business intelligence dashboard | Power BI (DAX measures, data model) |
| Notebook environment | Jupyter |

---

## 🧹 Data Cleaning Summary

The raw data required substantial cleaning before analysis was reliable. Key issues found and handled:

| Issue | Description | Fix |
|---|---|---|
| **Disguised missing values** | `expectedTime` / `actualTime` used `-1` as a placeholder for "no data" (99.9% of rows) | Recoded to `NaN`; excluded from delay analysis |
| **Misleading columns** | `deliveryStatusId` looked like a status field but was actually a near-unique sequential ID | Derived a real status field from timestamp presence/absence instead |
| **Zero-variance columns** | `deliveryType`, `preOrder` had a single constant value across all rows | Dropped |
| **Fully empty columns** | `deliveryParentId`, `readyDateTime`, `orderCampusId`, etc. were 95–100% null | Dropped |
| **Corrupted / column-shifted rows** | A small number of rows had values shifted into the wrong columns due to unescaped characters in the raw CSV | Identified via type-mismatch checks and removed |
| **Embedded newlines in text fields** | Some seller name fields contained literal line breaks, which broke row alignment in strict CSV parsers (e.g. Power BI) | Stripped from all text columns before export |
| **Logical/integrity errors** | ~8.5% of records showed a pickup timestamp *before* the confirmation timestamp | Flagged as a data-integrity issue and excluded from timing metrics |
| **Outliers** | A handful of "delivered" orders showed durations of months/years due to data errors | Capped to a realistic 0–24 hour window; excluded rows reported transparently |

Full methodology and reasoning for each decision is documented in [`docs/data_cleaning_error_types_interview_guide.md`](docs/data_cleaning_error_types_interview_guide.md).

---

## 📊 Key Business Insights

1. **~46% of all orders are abandoned before confirmation**, and 95%+ show no activity after creation — pointing to a problem at the order-placement step itself, not a slow follow-up process.
2. **The confirm-to-pickup stage accounts for ~40 of the ~60-minute median delivery time** — by far the largest single bottleneck in the fulfillment pipeline.
3. **The top 1% of buyers generate ~29% of all orders**; the top 10% generate over 76% — revenue is highly concentrated, making retention of top buyers more valuable than broad acquisition.
4. **Seller reliability varies more than 3x** among the highest-volume sellers (26.8%–88.6% delivered rate), independent of order volume — a clear, actionable list for operational review.
5. **Delivery speed does not explain one-time-buyer churn.** Buyers who ordered once and never returned had delivery times statistically indistinguishable from buyers who went on to become repeat customers — the cause likely lies outside delivery operations (pricing, product fit, engagement).
6. **Order volume peaked in 2021** and has since declined to roughly a third of its peak, with new-buyer acquisition also softening through 2025 — a retention/growth signal that a flat top-line number alone would hide.

---

## 🚀 How to Run

### 1. Jupyter Notebook (full cleaning + analysis pipeline)
```bash
pip install pandas numpy matplotlib
jupyter notebook notebooks/delivery_order_full_analysis.ipynb
```
Update the file paths in the first code cell to point at your local copies of the two raw CSVs, then run all cells top to bottom.

### 2. Streamlit Dashboard (interactive)
```bash
pip install streamlit pandas numpy plotly
cd dashboard
streamlit run streamlit_dashboard.py
```
Requires `orders_cleaned_for_powerbi.csv`, `delivery_cleaned_for_powerbi.csv`, and `seller_summary_for_powerbi.csv` to be in the same folder as the script (or update the paths inside `load_data()`).

### 3. Static HTML Report
Open `dashboard/business_analysis_dashboard.html` directly in any browser — no installation or server required.

### 4. Power BI Dashboard
Follow the step-by-step build guide in [`docs/powerbi_dashboard_guide.md`](docs/powerbi_dashboard_guide.md), which covers data import, relationships, DAX measures, and visual layout using the cleaned CSVs in `data/`.

---

## 📈 Dashboard Preview

*(Add a screenshot of your Streamlit app or Power BI dashboard here once uploaded — e.g.)*

```markdown
![Dashboard preview](docs/screenshots/dashboard_overview.png)
```

---

## 🔮 Possible Next Steps

- Root-cause investigation into the 46% order-abandonment rate (funnel/UX analysis)
- Cohort-based retention analysis to separate acquisition decline from engagement decline
- Seller-level operational audit for low-reliability, high-volume sellers
- A/B test on confirm-to-pickup workflow changes to validate the bottleneck fix

---

## 👤 Author

Krishna — B.Tech Chemical Engineering, MNNIT Allahabad
Project completed as part of independent data analytics practice, submitted for academic and professional review.

---

## 📄 License

This project is shared for educational and portfolio purposes. Add a license (e.g. MIT) here if you intend for others to reuse the code.
