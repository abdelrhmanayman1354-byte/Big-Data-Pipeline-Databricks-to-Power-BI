# 🏆 Data Catalog: Gold Layer (Star Schema)

## 📌 Overview
The **Gold Layer** represents the final, consumption-ready stage of the Medallion Architecture. The data is structured using a **Star Schema** model, optimized for Business Intelligence (BI), reporting, and advanced analytics. It consists of highly curated dimension tables and a central fact table.

---

## 🏗️ Entity Relationship Diagram (ERD)
The model follows a classic Star Schema where the `fact_orders` table is at the center, linked to dimensions via enforced **Surrogate Keys (SK)**.

---

## 📘 Table Definitions

### 1️⃣ gold.dim_products
**Purpose:** Provides a master list of all menu items and their attributes.

| Column Name | Data Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `product_skey` | **INT** | **PK** | Surrogate key uniquely identifying each product. |
| `item_name` | **STRING** | - | The name of the restaurant item (e.g., 'Cheeseburger'). |
| `category` | **STRING** | - | The high-level classification (e.g., 'Main Course', 'Dessert'). |
| `avg_unit_price`| **DOUBLE** | - | The calculated average unit price for the product. |

---

### 2️⃣ gold.dim_branches
**Purpose:** Contains information about the different restaurant branch locations.

| Column Name | Data Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `branch_skey` | **INT** | **PK** | Surrogate key uniquely identifying each branch. |
| `branch` | **STRING** | - | The official name/location of the restaurant branch. |

---

### 3️⃣ gold.dim_date
**Purpose:** A dedicated date dimension for time-series analysis and grouping.

| Column Name | Data Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `order_date` | **DATE** | **PK** | The actual calendar date (The joining key). |
| `year` | **INT** | - | Calendar year of the transaction. |
| `month` | **INT** | - | Numerical month (1-12). |
| `month_name` | **STRING** | - | Full month name (e.g., 'April'). |
| `day` | **INT** | - | Day of the month. |
| `day_name` | **STRING** | - | Day of the week (e.g., 'Friday'). |
| `is_weekend` | **STRING** | - | Flag indicating if the date is a weekend ('True'/'False'). |

---

### 4️⃣ gold.fact_orders
**Purpose:** The central table storing all transactional metrics and quantitative data.

| Column Name | Data Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `sales_skey` | **BIGINT** | **PK** | Unique primary key for each sale record. |
| `product_skey` | **INT** | **FK** | Foreign key linking to `dim_products`. |
| `branch_skey` | **INT** | **FK** | Foreign key linking to `dim_branches`. |
| `order_date` | **DATE** | **FK** | Foreign key linking to `dim_date`. |
| `hour` | **BIGINT** | - | The hour of the day the order was placed (0-23). |
| `quantity` | **BIGINT** | - | Total units sold for this item. |
| `price` | **DOUBLE** | - | Unit price at the time of transaction. |
| `discount` | **DOUBLE** | - | Total discount amount applied. |
| `total_amount` | **DOUBLE** | - | Final revenue after discount ($Qty \times Price - Discount$). |

---

## 🛠️ Data Integrity Rules
* **Surrogate Keys:** All joins between Fact and Dimension tables are performed using Integer-based keys for maximum query performance.
* **Referential Integrity:** Enforced Foreign Key constraints ensure no "orphan" records exist in the Fact table.
* **Nullability:** Primary Key columns are explicitly set to `NOT NULL` in Delta Lake.
