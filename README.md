# Restaurant-Food-Delivery-Analytics-Dashboard
# 🍽️ Restaurant & Food Delivery Analytics Dashboard
### A 6-Page Interactive Power BI Project

| | |
|---|---|
| **Student Name** | Chirag Gupta |
| **Registration Number** | PIET24CD020 |
| **Tool Used** | Microsoft Power BI Desktop (Power Query, DAX, Data Modeling) |
| **Project Type** | Business Intelligence / Data Visualization |

---

## 📑 Table of Contents
1. [Project Title](#1-project-title)
2. [Dataset Description](#2-dataset-description)
3. [Objectives](#3-objectives)
4. [Data Cleaning Performed](#4-data-cleaning-performed)
5. [Data Model](#5-data-model)
6. [DAX Measures](#6-dax-measures)
7. [Dashboard Screenshots](#7-dashboard-screenshots)
8. [Key Findings](#8-key-findings)
9. [Conclusion](#9-conclusion)

---

## 1. Project Title
**Restaurant & Food Delivery Analytics Dashboard** – an end-to-end Power BI report that analyses revenue, customers, restaurant quality and delivery operations for restaurants across Bengaluru localities.

---

## 2. Dataset Description
The project combines restaurant listing data with customer order data. It covers **~200K orders, ~20K customers and ~56K restaurant records** across multiple Bengaluru localities (Banashankari, BTM, Koramangala, Indiranagar, Whitefield, Church Street, etc.) for the years **2023 – 2025**.

| Category | Key Columns |
|---|---|
| **Restaurant** | `name`, `PrimaryCuisine`, `rate` (rating), `votes`, `Cost_Bucket` (Budget / Mid Range / Premium), `ServiceType` (Cafes, Desserts, Delivery, Dine-out, Drinks & nightlife, Pubs and bars, Buffet), `Online Order`, `Table Booking` |
| **Customer** | `CustomerID`, `CustomerType` (New / Repeat), `Signup Date / Year` |
| **Order** | `OrderID`, `Order Date`, `Revenue`, `Order Status` (Delivered / Delayed / Cancelled), `Delivery Status` (On Time / Late), `Delivery Time` (minutes) |
| **Location** | `FilterCity` (locality / area) |

---

## 3. Objectives
1. Build a **single executive view** of revenue, orders, average order value and delivery time.
2. Understand **customer behaviour** – new vs repeat customers, orders per customer and top-revenue customers.
3. Evaluate **restaurant performance** by cuisine, rating, cost bucket and revenue.
4. Monitor **delivery & operations** – delays, late orders, cancellations and city-wise delivery time.
5. Provide **interactive exploration** through slicers, drill-through, a KPI selector (field parameter) and Q&A.
6. Convert raw data into **actionable business insights**.

---

## 4. Data Cleaning Performed
Data preparation was done in **Power Query Editor** before modelling:

- Removed duplicate records and blank / null rows.
- Standardised column data types (dates, whole numbers, decimals, text).
- Cleaned the **rating** column (e.g. removed text such as `"/5"`, `"NEW"`, `"-"`) and converted it to a numeric `rate` field.
- Trimmed and standardised text values (restaurant names, cuisines, city/locality names).
- Extracted the **primary cuisine** from multi-cuisine strings into `PrimaryCuisine`.
- Created the **`Cost_Bucket`** column (Budget / Mid Range / Premium) from the approximate cost-for-two field.
- Created a **`FilterCity`** column to give clean locality names for slicers and charts.
- Derived `CustomerType`, `Year`, and delivery status (On Time / Late) fields.
- Handled missing values (replaced with defaults or filtered out where unusable).
- Renamed columns and tables with business-friendly names.

> ⚠️ *Note:* a few records show values such as `('Rated 4.0'` in the restaurant `name` field (visible in the Top 10 Restaurants table). This is a parsing artefact from the source data and is listed under *Future Improvements*.

---

## 5. Data Model
A **star-schema** model was used: fact tables at the centre, descriptive dimension tables around them, joined by one-to-many relationships.

```
                 ┌──────────────┐
                 │  Customers   │
                 │ (CustomerID) │
                 └──────┬───────┘
                        │ 1
                        │
                        ▼ *
┌──────────────┐ 1   ┌──────────┐  *   ┌──────────────┐
│ Restaurants  │────▶│  Orders  │◀─────│  Date Table  │
│(RestaurantID)│  *  │  (Fact)  │  1   │ (Year/Month) │
└──────────────┘     └──────────┘      └──────────────┘
                        ▲ *
                        │
                        │ 1
                 ┌──────┴───────┐
                 │  Location    │
                 │ (FilterCity) │
                 └──────────────┘
```

- **Fact table:** Orders (revenue, status, delivery time)
- **Dimensions:** Customers, Restaurants, Location, Date
- **Helper tables:** `KPI Name` (field parameter used for the KPI selector on the *Advanced Insights* page)
- **Relationships:** Single-direction filtering, one-to-many.

---

## 6. DAX Measures
Core measures used across the dashboard:

```DAX
-- ===== Core KPIs =====
Total Revenue = SUM(Orders[Revenue])

Total Orders = COUNTROWS(Orders)

Avg Order Value = DIVIDE([Total Revenue], [Total Orders])

Avg Delivery Time = AVERAGE(Orders[DeliveryTime])

-- ===== Customer Metrics =====
Total Customers = DISTINCTCOUNT(Orders[CustomerID])

Repeat Customers =
CALCULATE(
    DISTINCTCOUNT(Orders[CustomerID]),
    Customers[CustomerType] = "Repeat"
)

Repeat Customer % = DIVIDE([Repeat Customers], [Total Customers])

Orders Per Customer = DIVIDE([Total Orders], [Total Customers])

-- ===== Restaurant Metrics =====
Total Restaurants = DISTINCTCOUNT(Restaurants[name])

Average Rating = AVERAGE(Restaurants[rate])

Average Votes = AVERAGE(Restaurants[votes])

Revenue per Restaurant = DIVIDE([Total Revenue], [Total Restaurants])

-- ===== Delivery & Operations =====
Late Orders =
CALCULATE([Total Orders], Orders[DeliveryStatus] = "Late")

Cancelled Orders =
CALCULATE([Total Orders], Orders[OrderStatus] = "Cancelled")

Late Order % = DIVIDE([Late Orders], [Total Orders])

Cancellation Rate = DIVIDE([Cancelled Orders], [Total Orders])
```

> 📝 Column and table names above are representative – adjust them to match the exact names in your `.pbix` file.

The **KPI selector** on the *Advanced Insights* page uses a **field parameter** (`KPI Name`) so the user can switch the charts between *Average Delivery Time, Orders, Average Order Value* and *Revenue*.

---

## 7. Dashboard Screenshots
The report has **6 pages**, designed with a consistent red-and-white theme, KPI cards at the top and detailed visuals beneath.

### Page 1 – Executive Overview
High-level KPIs (Revenue, Orders, AOV, Delivery Time), revenue by city, order-status breakdown, Top 10 restaurants, revenue/order trends and a city-wise filter.

![Executive Overview](images/01_executive_overview.png)

### Page 2 – Customer Analytics
Total / repeat customers, repeat %, orders per customer, orders by customer type, new vs repeat split, top-10 customers by revenue, signup trend and table-booking slicer.

![Customer Analytics](images/02_customer_analytics.png)

### Page 3 – Restaurant Performance
Total restaurants, average rating and votes, revenue per restaurant, orders by cuisine, rating-wise revenue, online-order slicer, average rating by cuisine, orders per cost bucket and revenue by top 10 restaurants.

![Restaurant Performance](images/03_restaurant_performance.png)

### Page 4 – Delivery and Operations
Delivery time, late and cancelled orders, city-wise delivery time and cancellations, average order value by service type, city-wise delivery details and delivery-status breakdown with a year slicer.

![Delivery and Operations](images/04_delivery_operations.png)

### Page 5 – Advanced Insights
A KPI selector that dynamically changes the trend chart and the rating-vs-KPI scatter plot by cost bucket, plus a Q&A visual for natural-language questions.

![Advanced Insights](images/05_advanced_insights.png)

### Page 6 – Restaurant Details (Drill-through)
A drill-through page that shows detailed KPIs, customer-type orders, delivery status and revenue/order trends for any selected restaurant (example: *Cafe Coffee Day*).

![Restaurant Details](images/06_restaurant_details.png)

---

## 8. Key Findings

### 💰 Business Performance
- The business generated **₹165M in revenue from 200K orders**, with an **average order value of ≈ ₹824.57**.
- Revenue is concentrated in a few localities – the top city contributes about **15M**, nearly double the next one (≈ 8M).
- The monthly revenue trend stays stable in the **4.6M – 5.3M** range, with a sharp drop in the final period (4.26M), likely due to incomplete data for that period.

### 📦 Orders & Delivery
- **80.08%** of orders are **delivered**, while **9.94%** are delayed and **9.98%** are cancelled.
- Average delivery time is **44.55 minutes**; city-wise averages range from about **45 to 48.6 minutes**.
- **BTM has the highest number of cancellations (1,876)** – roughly double the next locality – making it the top area for operational improvement.
- The delivery status chart shows roughly **half of deliveries arrive late** (≈ 48%), indicating a clear SLA/logistics gap.

### 👥 Customers
- There are **20K customers** and **50.28% are repeat customers**, with a nearly even new-vs-repeat split.
- Each customer places **10 orders on average**, which indicates strong engagement.
- Top-10 customers each generate roughly **₹19K–21K** in revenue with 18–23 orders.

### 🍴 Restaurants & Cuisine
- Average restaurant rating is **3.70** with an average of **261 votes**.
- The top cuisine accounts for **44K orders**, far ahead of the next (≈ 18K).
- **Budget** restaurants drive **59.1%** of orders, **Mid Range 28.5%** and **Premium 12.4%**.
- Revenue peaks for restaurants rated around **3.8–4.2**; very low and very high ratings generate less revenue.
- Average order value is highest for **Cafes (₹835)** and lowest for **Buffet (₹819)** – a narrow range across service types.

### 🔎 Example – Cafe Coffee Day
- ₹277K revenue, 325 orders, rating 3.26, average delivery time 43.69 min, and about 47% late deliveries – a high-volume brand with below-average rating and delivery reliability.

---

## 9. Conclusion
This project shows how Power BI can turn raw restaurant and order data into a complete decision-support tool. The six-page dashboard covers executive KPIs, customer behaviour, restaurant quality, delivery operations, advanced KPI exploration and restaurant-level drill-through.

The analysis shows that the business is **revenue-stable with strong customer loyalty (50% repeat rate)** but faces **operational challenges** – nearly half of deliveries are late and ~10% of orders are cancelled, particularly in high-volume localities like BTM. Focusing on delivery logistics, improving ratings of high-volume restaurants and promoting mid-range / premium options can increase both customer satisfaction and revenue.

### 🔮 Future Improvements
- Fix parsing errors in the restaurant `name` field (values like `('Rated 4.0'`).
- Verify the label of the *Avg Order Value* card on the Delivery page (it currently shows a percentage – it is probably a late-order %) and align late-order counts between cards and charts.
- Add forecasting for revenue and orders.
- Add peak-hour / day-of-week analysis and customer segmentation (RFM).
- Publish to Power BI Service with scheduled refresh and row-level security.

---

## 🛠️ Tech Stack
`Power BI Desktop` · `Power Query (M)` · `DAX` · `Star-Schema Data Modeling` · `Field Parameters` · `Drill-through` · `Q&A`

## 👤 Author
**Chirag Gupta** – Reg. No. **PIET24CD020**
