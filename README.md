# Bank Marketing Sales Funnel & Conversion Optimization

## Executive Overview
This project provides an end-to-end data analytics and business intelligence framework designed to optimize a retail banking telemarketing operation. By evaluating 41,188 prospect records across multiple campaign stages, the analysis identifies key operational bottlenecks, contact fatigue patterns, channel conversion differentials, high-value demographic segments, and seasonality anomalies.

The ultimate deliverable is a high-impact **Tableau Public Dashboard** supported by custom calculated fields and data engineering governance to improve baseline subscription conversion rates while reducing operational expenditure.

---

## Key Performance Indicators & Core Findings

### 1. Overall Baseline & Contact Fatigue (Stage 1)
* **Baseline Conversion Rate (CR):** **11.26%** (5,182 subscriptions out of 41,188 prospects).
* **Primary Friction Point (Drop-off):** Over **21% of prospects** undergo more than 3 contact attempts.
* **Commercial Fatigue Threshold:** Conversion probability drops below **6%** after the 3rd contact attempt, destroying campaign profitability and burning the lead database.

### 2. Channel & Demographic Segmentation (Stage 2)
* **Contact Channel Impact:** **Cellular/Mobile** channels achieve a **~14.7% CR** vs. **~5.2%** for traditional Landline/Telephone numbers.
* **High-Value Customer Profiles:** **Students** and **Retired** individuals convert at over **25%** (compared to the ~10% average across other occupations).

### 3. Re-Engagement & Seasonality Anomalies (Stage 3)
* **Past Campaign Success (`poutcome = success`):** Returning prospects with previous positive engagement achieve a **>65% conversion rate**.
* **Warm Leads (`pdays < 999`):** Re-contacted warm leads yield a **>60% CR** vs. **9.2% for cold calling**.
* **Seasonality Disconnect:** High-volume outreach months (May, July, August) exhibit low conversion rates (~8-10%). Conversely, under-leveraged months (March, September, October, December) display exceptionally high conversion rates (>40-50%).

---

## Data Architecture & Tableau Calculated Fields

To translate raw campaign attributes into actionable decision-making rules, the following calculated fields must be created within **Tableau Desktop / Tableau Public**:

### 1. Contact Fatigue Threshold (`Contact Threshold Status`)
Categorizes prospects based on contact frequency to enforce the 3-call capping rule:
```tableau
IF [campaign] <= 3 THEN "Optimal (<= 3 Calls)"
ELSE "Over-contacted (> 3 Calls)"
END
```

### 2. Subscription Conversion Rate (`Conversion Rate`)
Calculates the dynamic percentage of successful campaign conversions:
```tableau
SUM(IF [y] = "yes" THEN 1 ELSE 0 END) / COUNT([y])
```

### 3. Channel Prioritization Flag (`Preferred Channel Flag`)
Isolates high-converting mobile phone contacts:
```tableau
IF LOWER([contact]) = "cellular" OR LOWER([contact]) = "mobile" THEN "High Priority (Mobile)"
ELSE "Standard Priority (Landline)"
END
```

### 4. Customer Segment Priority (`Customer Segment Priority`)
Isolates top-performing demographic segments:
```tableau
IF LOWER([job]) = "student" OR LOWER([job]) = "retired" THEN "Tier 1: High Value (>25% CR)"
ELSE "Tier 2: Standard Profile"
END
```

### 5. Lead Re-engagement Temperature (`Lead Temperature`)
Differentiates hot/warm leads from cold prospects:
```tableau
IF [poutcome] = "success" THEN "Hot Lead (Past Success >65% CR)"
ELSEIF [pdays] < 999 AND [pdays] >= 0 THEN "Warm Lead (Recent Contact >60% CR)"
ELSE "Cold Lead (New Prospect)"
END
```

### 6. Seasonal Efficiency Tier (`Seasonal Efficiency Tier`)
Identifies high-yield months vs. oversaturated outreach periods:
```tableau
IF IN(LOWER([month]), "mar", "sep", "oct", "dec") THEN "High Conversion Month (>40%)"
ELSEIF IN(LOWER([month]), "may", "jul", "aug") THEN "Low Conversion / High Volume"
ELSE "Moderate Conversion Month"
END
```

---

## Tableau Dashboard Architecture

The dashboard is structured into 4 interactive worksheets organized within a single executive layout:

| Worksheet | Visual Type | Dimensions & Measures Used | Business Objective |
| :--- | :--- | :--- | :--- |
| **1. Executive KPI Summary** | Banner / KPI Cards | `Conversion Rate`, `Contact Threshold Status`, `Conversion Volume` | Instant high-level health check of campaign efficiency. |
| **2. Fatigue & Channel Breakdown** | Bar Chart / Side-by-Side | `Preferred Channel Flag`, `Contact Threshold Status`, `Conversion Rate` | Visualizing drop-off after 3 calls and mobile superiority. |
| **3. Lead Temperature Matrix** | Highlight Table / Heatmap | `Lead Temperature`, `Customer Segment Priority`, `Conversion Rate` | Demonstrating the 60%+ CR of warm leads and high-value profiles. |
| **4. Seasonality & Volume Alignment** | Dual-Axis Combo Chart | `Month`, `Call Volume (Count)`, `Conversion Rate` | Uncovering month-by-month sales disconnects. |

---

## Actionable Recommendations & Implementation Roadmap

1. **Enforce Automated Call Capping (P1):** Configure dialer software to drop prospects after 3 unsuccessful attempts, reducing wasted call capacity by 21%.
2. **Prioritize Mobile & Hot Leads (P1):** Route all `Cellular` contacts and `poutcome = success` warm leads to senior agents first.
3. **Reallocate Monthly Outreach Budgets (P2):** Shift marketing budget and agent capacity from low-performing summer months (May, July, August) to Q1/Q4 high-performing periods (March, September, October, December).

---

## Repository Structure
```
├── README.md                           <- Technical documentation & project overview
├── data/
│   └── bank+marketing      <- Source dataset 
├── dashboard/
│   └── TASK-03-FUTURE-DS.twbx <- Tableau Packaged Workbook
    └── DASHBOARD.jpeg
└── report/
    └── Recommendations_Document.pdf   <- Executive PDF summary document
├── notebook.ipynb
```