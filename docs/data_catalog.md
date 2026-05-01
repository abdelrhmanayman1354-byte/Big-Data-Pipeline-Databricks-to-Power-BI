# 🏆 Data Catalog: Gold Layer (Star Schema)

## 📌 Project Overview
The **Gold Layer** is the final analytical stage of the data pipeline, optimized for **Business Intelligence (BI)** and high-performance reporting. It features a robust **Star Schema** that supports advanced time-series analysis, customer behavior tracking, and geospatial insights.

---

## 🏛️ Star Schema Architecture
The model is built on a central **Fact Table** surrounded by **6 Dimension Tables**, ensuring efficient data retrieval and clear analytical boundaries.

*   **Fact Table:** `fact_orders` (Centralized transactional metrics).
*   **Dimensions:** Contextual tables for Products, Dates, Branches, Customers, Payment, and Order Types.
*   **Relationships:** One-to-Many ($1:\infty$) using optimized **Surrogate Keys (SK)**.

---

## 📘 Table Definitions

### 1️⃣ gold.fact_orders (The Engine)
*Stores all quantitative metrics and transactional keys.*

| Column Name | Data Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `sales_skey` | **BIGINT** | **PK** | Unique identifier for each sales record. |
| `branch_skey` | **INT** | **FK** | Foreign Key to `dim_branches`. |
| `product_skey` | **INT** | **FK** | Foreign Key to `dim_products`. |
| `order_skey` | **INT** | **FK** | Foreign Key to `dim_order_type`. |
| `payment_skey` | **INT** | **FK** | Foreign Key to `dim_payment`. |
| `customer_id` | **STRING** | **FK** | Foreign Key to `dim_customers`. |
| `order_date` | **DATE** | **FK** | Foreign Key to `dim_date`. |
| `order_id` | **STRING** | - | Business ID for the overall order. |
| `hour` | **INT** | - | Transaction hour (0-23). |
| `quantity` | **INT** | - | Number of units sold. |
| `price` | **DOUBLE** | - | Unit price at transaction time. |
| `discount` | **DOUBLE** | - | Total discount applied. |
| `total_amount` | **DOUBLE** | - | Net revenue ($Qty \times Price - Discount$). |
| `rating` | **INT** | - | Customer feedback score (1-5). |

---

### 2️⃣ gold.dim_date (Temporal Intelligence)
*Enables deep time-series analysis and period-over-period comparisons.*

| Column Name | Key | Description |
| :--- | :--- | :--- |
| `order_date` | **PK** | Primary date key. |
| `year` | - | Calendar Year. |
| `month` | - | Month Number (1-12). |
| `month_name` | - | Full Month Name (e.g., "May"). |
| `day` | - | Day of the Month. |
| `day_name` | - | Full Weekday Name (e.g., "Friday"). |
| `day_of_week_number`| - | Numeric Day (1=Sat or 1=Mon based on config). |
| `day_part` | - | Time Category: **Breakfast, Lunch, Afternoon, Dinner, Late Night**. |
| `is_weekend` | - | Boolean flag (True/False). |

---

### 3️⃣ gold.dim_products
| Column Name | Key | Description |
| :--- | :--- | :--- |
| `product_skey` | **PK** | Surrogate key for product master. |
| `item_name` | - | Name of the dish or beverage. |
| `category` | - | High-level menu category. |
| `avg_unit_price` | - | Calculated average price of the item. |
| `photo_url` | - | Image link for visual dashboards. |

---

### 4️⃣ gold.dim_customers (Segmentation)
| Column Name | Key | Description |
| :--- | :--- | :--- |
| `customer_id` | **PK** | Unique ID for the customer. |
| `customer_segment` | - | **VIP, Loyal, New, At Risk, Lost**. |
| `days_gap` | - | Days since the last order (Recency). |
| `days_gap (bins)` | - | Grouped bins for frequency analysis. |
| `last_order_date` | - | The customer's most recent activity date. |
| `total_orders` | - | Lifetime order count. |

---

### 5️⃣ gold.dim_branches (Geospatial)
| Column Name | Key | Description |
| :--- | :--- | :--- |
| `branch_skey` | **PK** | Surrogate key for branch location. |
| `branch` | - | Name of the restaurant branch. |
| `province_en` | - | Geographic province (English). |
| `wkt_geometry` | - | WKT format for map visualizations. |

---

### 6️⃣ gold.dim_payment & gold.dim_order_type
| Table | Columns | Description |
| :--- | :--- | :--- |
| **dim_payment** | `payment_skey`, `payment_method` | Cash, Credit Card, Wallet, etc. |
| **dim_order_type**| `order_skey`, `order_type` | Dine-in, Delivery, Pickup. |


---

## 🛠️ Data Integrity & Performance
*   **Delta Lake:** Built on Delta tables for ACID compliance.
*   **No Nulls:** Critical keys (PK/FK) are enforced to be `NOT NULL`.
*   **Surrogate Keys:** Integer-based keys are used for high-efficiency joins.
