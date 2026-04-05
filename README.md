# 📊 Receipt-o-matic

### Automated Expense Tracking & Price Intelligence Dashboard

---

## 🚀 Overview

Receipt-o-matic is an end-to-end automated data pipeline that transforms receipt images into structured, actionable financial insights.

The system combines OCR, automation workflows, cloud-based storage, and business intelligence to enable detailed expense tracking and product-level analysis.

---

## 🧠 Key Features

* 📸 Receipt ingestion via image upload
* 🔍 OCR-based data extraction (receipt parsing)
* 🧾 Product-level expense tracking
* 🏷 Rule-based product categorization with priority logic
* 🏪 Store-level price comparison
* 📊 Interactive Power BI dashboard
* ⛽ Manual expense integration (e.g., gas, subscriptions)

---

## 🏗 Architecture

```
[Google Drive]
      ↓
[Make Automation]
      ↓
[Taggun OCR API]
      ↓
[Google Sheets]
      ↓
[Power BI Dashboard]
```

---

## ⚙️ Tech Stack

* **Automation:** Make (low-code workflow automation)
* **OCR API:** Taggun Receipt OCR
* **Storage:** Google Sheets
* **Data Modeling & Visualization:** Power BI

---

## 🔄 Data Flow

1. A receipt image is uploaded to Google Drive
2. Make detects the new file (event trigger)
3. The image is sent to Taggun OCR API via HTTP request
4. Extracted data (total, date, merchant, products) is returned
5. Data is cleaned and structured
6. Results are stored in Google Sheets
7. Power BI connects to Sheets and updates the dashboard

---

## 🗂 Data Model

### Tables

**Receipts**

* trackingId
* Date
* Merchant
* Total

**Products**

* trackingId
* Product
* Quantity
* UnitPrice
* TotalProduct
* Category (calculated)

**ManualExpenses**

* Date
* Category
* Description
* Amount

**Categories (mapping table)**

* Keyword
* StandardCategory
* Priority

---

## 🧩 Categorization Logic

Products are categorized using a keyword-based matching system with priority levels:

* Priority 1 → specific keywords (e.g., "sauce", "bbq")
* Priority 2 → medium specificity
* Priority 3 → generic terms (e.g., "poulet", "boeuf")

The system selects the **highest-priority match** using DAX:

```DAX
Category = 
VAR txt = LOWER(Products[Product])
VAR match =
    TOPN(
        1,
        FILTER(
            Categories,
            SEARCH(Categories[Keyword], txt, 1, 0) > 0
        ),
        Categories[Priority], ASC
    )
RETURN
COALESCE(
    MAXX(match, Categories[StandardCategory]),
    "UNCATEGORIZED"
)
```

---

## 📊 Dashboard Features

* Total expenses (KPI)
* Expense distribution by category (pie chart)
* Monthly spending trends (line chart)
* Store price comparison (bar chart)
* Product-level detail table

---

## 📸 Screenshots

* Dashboard overview
* Category distribution
* Price comparison by store
* Automation workflow (Make)

---

## 🔧 Setup Instructions

### 1. Automation (Make)

* Create a scenario with:

  * Google Drive (watch files)
  * Google Drive (download file)
  * HTTP module (Taggun OCR API)
  * Google Sheets (add rows)

* Optional:

  * Import `/docs/make_blueprint.json`

---

### 2. Google Sheets

Create 4 sheets:

* Receipts
* Products
* ManualExpenses
* Categories

---

### 3. Power BI

* Connect to Google Sheets (via web/CSV)

* Create relationships:

  * Products[trackingId] → Receipts[trackingId]

* Add calculated columns and measures

* Build dashboard visuals

---

## 📁 Repository Structure

```
receipt-o-matic/
│
├── dashboard/
│   └── receipt-o-matic.pbix
│
├── data/
│   └── sample_data.xlsx
│
├── screenshots/
│   ├── dashboard.png
│   ├── make_scenario.png
│
├── docs/
│   ├── architecture.png
│   └── make_blueprint.json
│
└── README.md
```

---

## ⚠️ Notes

* Sample data is included (no personal or sensitive information)
* API keys and credentials are NOT included
* Blueprint file may require manual reconnection of services

---

## 💡 Future Improvements

* Automatic product categorization using ML
* Price trend tracking over time
* Cheapest store recommendation system
* Spending alerts and anomaly detection
* Mobile-friendly interface

---

## 👤 Authors

- Sebastian Garcia (https://github.com/eloogia) – Data Analysis, Data Modeling, Power BI
- Luisa Vargas (https://github.com/lxvargasg) – Data Modeling, Data Cleaning, Validation

Personal project demonstrating an end-to-end data pipeline combining OCR, automation, data modeling, and business intelligence. 2026
