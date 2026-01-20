# Data Catalog for Gold Layer

## Overview
The Gold Layer is the business-level data representation, structured to support analytical and reporting use cases. It consists of **dimension tables**, **fact tables** and a denormalized **view** for specific business metrics.

---

### 1. **gold.gld_dim_customers**
- **Purpose:** Stores customer details enriched with demographic and geographic data.

| Column Name      | Data Type     | Description                                                                                   |
|------------------|---------------|-----------------------------------------------------------------------------------------------|
| customer_id      | String           | Unique numerical identifier assigned to each customer.                                        |
| country  | String  |     Full name of the country where the customer is located.     |
| country_code      | String  |       Standardized country code representing the customer’s country.                                   |
| state        | String  |     State or province associated with the customer’s address.                                                |
| region          | String  | Business-defined geographic region used for regional reporting and analysis.                              |
| phone   | String  |       Customer’s contact phone number       |
| file_name           | String  |    Name of the source file from which the customer record was ingested, used for data lineage and traceability.                              |
| ingest_timestamp        | Timestamp          |      Timestamp indicating when the record was ingested into the pipeline, used for audit and freshness tracking.                        |

---

### 2. **gold.gld_dim_products**
- **Purpose:** Contains master data about products and their descriptive attributes.

| Column Name         | Data Type     | Description                                                                                   |
|---------------------|---------------|-----------------------------------------------------------------------------------------------|
| product_id          | String           | A unique identifier assigned to the product for internal tracking and referencing.            |
| sku     | String  | Stock Keeping Unit used to uniquely identify a product in sales and inventory systems. |
| category_code        | String  |    Code representing the product category.     |
| category_name         | String |   Name of the product category.  |
| brand_code            | String  | Code identifying the product brand. |
| brand_name         | String  |   Name of the brand associated with the product.    |
| color | String  |     Color of the product.                |
| size           | String   |    Size of the product, stored as text to support different sizing formats.    |
| material        | String  |  Primary material from which the product is made.   |
| weight_grams          | Int         | Weight of the product expressed in grams. |
| length_cm | Float | Length of the product in centimeters. |
| width_cm | Float | Width of the product in centimeters. |
| height_cm | Float | Height of the product in centimeters. |
| rating_count| Int | Number of customer ratings submitted for the product. |
| file_name | String | Name of the source file from which the customer record was ingested, used for data lineage and traceability.    |
| ingest_timestamp| Timestamp |  Timestamp indicating when the record was ingested into the pipeline, used for audit and freshness tracking. |

---

### 3. **gold.gld_dim_date**
- **Purpose:** Provides a calendar reference table used for time-based analysis.

| Column Name         | Data Type     | Description                                                                                   |
|---------------------|---------------|-----------------------------------------------------------------------------------------------|
| date_id          | Int           |    Surrogate key representing the date, formatted as YYYYMMDD.      |
| date     | Date  | Full calendar date. |
| year        | Int  |   Calendar year extracted from the date.    |
| month_name         | String |  Name of the calendar month.   |
| is_weekend            | Int  | Indicator flag showing whether the date falls on a weekend (1 = weekend, 0 = weekday). |
| quarter         | String  |  Calendar quarter of the year.     |
| week_of_year | String  |     Week number within the year based on the calendar standard used.             |
| _ingested_at           | Timestamp         |        Timestamp indicating when the record was ingested into the pipeline, used for audit and freshness tracking.                    |
| _source_file        | String  |  Name of the source file from which the customer record was ingested, used for data lineage and traceability.  |

---

### 4. **gold.gld_fact_order_items**
- **Purpose:** Stores transactional sales data at the individual transaction level, used for revenue analysis.

| Column Name         | Data Type     | Description                                                                                   |
|---------------------|---------------|-----------------------------------------------------------------------------------------------|
| transaction_id         | String |  Unique identifier of the transaction.  |
| transaction_date     | Date  | Calendar date on which the transaction occurred. |
| transaction_ts        | Timestamp  |  Exact timestamp when the transaction was recorded.   |
| seq_no            | Int  | Sequence number of the transaction event, used to maintain ordering when multiple events share the same timestamp. |
| customer_id         | String  |   Identifier of the customer associated with the transaction.    |
| product_id | String  |         Identifier of the product involved in the transaction.         |
| date_id          | Int           |  Surrogate key linking the transaction to the date dimension.       |
| channel | String | Sales channel through which the transaction was made (e.g., website, mobile app). |
| coupon_code | String | Promotional coupon code applied to the transaction, if any. |
| coupon_flag | Int | Indicator showing whether a coupon was applied (1 = yes, 0 = no). |
| unit_price_currency | String | Currency code of the unit price (e.g., USD, EUR). |
| quantity | Int | Number of product units purchased in the transaction. |
| unit_price | Double | Price per unit before discounts and taxes. |
| gross_amount | Double | Total transaction amount before discounts and taxes. |
| discount_percent | Double | Percentage discount applied to the transaction. |
| discount_amount | Bigint | Monetary value of the discount applied. |
| tax_amount | Double |  Tax amount applied to the transaction.|
| net_amount | Double | Final transaction amount after discounts and taxes, in the original currency. |
| net_amount_usd | bigint | Net transaction amount converted to USD for standardized reporting. |

---

### 5. **gold.fact_transactions_denorm**
- **Purpose:** Provides a denormalized view of transactional data by combining transaction facts with related customer, product, and date attributes, simplifying analytical queries and enabling easy consumption by BI tools without requiring complex joins.

| Column Name         | Data Type     | Description                                                                                   |
|---------------------|---------------|-----------------------------------------------------------------------------------------------|
| transaction_id         | String |  Unique identifier of the transaction.  |
| transaction_date     | Date  | Calendar date on which the transaction occurred. |
| transaction_ts        | Timestamp  |  Exact timestamp when the transaction was recorded.   |
| seq_no            | Int  | Sequence number of the transaction event, used to maintain ordering when multiple events share the same timestamp. |
| customer_id         | String  |   Identifier of the customer associated with the transaction.    |
| product_id | String  |         Identifier of the product involved in the transaction.         |
| date_id          | Int           |  Surrogate key linking the transaction to the date dimension.       |
| channel | String | Sales channel through which the transaction was made (e.g., website, mobile app). |
| coupon_code | String | Promotional coupon code applied to the transaction, if any. |
| coupon_flag | Int | Indicator showing whether a coupon was applied (1 = yes, 0 = no). |
| unit_price_currency | String | Currency code of the unit price (e.g., USD, EUR). |
| quantity | Int | Number of product units purchased in the transaction. |
| unit_price | Double | Price per unit before discounts and taxes. |
| gross_amount | Double | Total transaction amount before discounts and taxes. |
| discount_percent | Double | Percentage discount applied to the transaction. |
| discount_amount | Bigint | Monetary value of the discount applied. |
| tax_amount | Double |  Tax amount applied to the transaction.|
| net_amount | Double | Final transaction amount after discounts and taxes, in the original currency. |
| net_amount_usd | bigint | Net transaction amount converted to USD for standardized reporting. |
| year| Int | Calendar year extracted from the date.  |
| month_name| String | Name of the calendar month. |
| day_name| String | Name of the day in the week.|
| is_weekend| Int | Indicator flag showing whether the date falls on a weekend (1 = weekend, 0 = weekday). |
| quarter         | String  |  Calendar quarter of the year.     |
| week | String  |     Week number within the year based on the calendar standard used.             |
| sku     | String  | Stock Keeping Unit used to uniquely identify a product in sales and inventory systems. |
| category_code        | String  |    Code representing the product category.     |
| category_name         | String |   Name of the product category.  |
| brand_code            | String  | Code identifying the product brand. |
| brand_name         | String  |   Name of the brand associated with the product.    |
| color | String  |     Color of the product.                |
| size           | String   |    Size of the product, stored as text to support different sizing formats.    |
| rating_count| Int | Number of customer ratings submitted for the product. |
| hour_of_day| Int | Hour of the day extracted from the transaction timestamp, represented as an integer |
