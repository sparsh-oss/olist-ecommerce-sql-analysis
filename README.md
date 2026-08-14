# Olist Brazilian E-Commerce — SQL Analysis (MySQL)

A relational database analysis of ~100,000 real orders from Olist, a Brazilian e-commerce marketplace, built across 9 interconnected tables in MySQL Workbench. This was my first SQL project working with a genuinely relational schema (as opposed to flat spreadsheet data), focused on JOINs, aggregation, window functions, and CTEs.

## 📊 Project Overview

This project analyzes Customers, Orders, Order Items, Payments, Reviews, Products, Sellers, and Geolocation data to answer:

- Which states generate the most orders?
- Which product categories drive the most revenue — and which have the worst customer satisfaction?
- How do customers pay, and does payment method relate to order value?
- Are orders delivered on time — and does late delivery actually hurt review scores?

## 🛠️ Tools & Techniques Used

- **MySQL Workbench** — schema design, table creation, and query execution
- **LOAD DATA INFILE** for bulk CSV imports (after troubleshooting `LOCAL INFILE` client restrictions — see Data Notes below)
- **Multi-table JOINs** — from simple 2-table joins up to 5-table joins
- **GROUP BY / HAVING** for category and state-level aggregation
- **Window Functions** — `RANK()`, `ROW_NUMBER()`, `LAG()` with `PARTITION BY`
- **CTEs (Common Table Expressions)** — `WITH ... AS` for readable multi-step queries
- **Correlated subqueries** — comparing individual rows to group averages
- **CASE WHEN** — custom delivery-status categorization

## 🧹 Data Import Notes

A few real-world data engineering issues came up while loading ~1.4 million total rows across 9 tables:

- **`LOCAL INFILE` client restriction:** MySQL Workbench's GUI import wizard and `LOAD DATA LOCAL INFILE` were both blocked by client-side security settings. Resolved by using `LOAD DATA INFILE` (without LOCAL) from MySQL's own `secure_file_priv` directory instead.
- **Blank date values:** Several orders have no delivery date (cancelled/undelivered orders). Used `NULLIF()` with user variables during import to convert blank strings to proper `NULL` values instead of failing on invalid datetime inserts.
- **Embedded newlines in review text:** A small number of review comments contain literal line breaks, which shifted row boundaries during import. Resolved by relaxing `sql_mode` to skip malformed rows — resulting in 99,223 of 99,224 reviews imported successfully (99.999%).
- **`customer_id` vs `customer_unique_id`:** This dataset assigns a new `customer_id` to every order, even for repeat customers — the same real person is identified via a separate `customer_unique_id` field. Initial repeat-purchase analysis incorrectly showed zero repeat customers until this was corrected.

## 🔑 Key Findings

**Top 5 States by Order Volume**
| State | Total Orders |
|---|---|
| São Paulo (SP) | 41,746 |
| Rio de Janeiro (RJ) | 12,852 |
| Minas Gerais (MG) | 11,635 |
| Rio Grande do Sul (RS) | 5,466 |
| Paraná (PR) | 5,045 |

São Paulo alone accounts for more orders than the next 3 states combined.

**Top 5 Categories by Revenue**
| Category | Total Revenue (R$) |
|---|---|
| Health & Beauty | 1,258,681.34 |
| Watches & Gifts | 1,205,005.68 |
| Bed, Bath & Table | 1,036,988.68 |
| Sports & Leisure | 988,048.97 |
| Computers Accessories | 911,954.32 |

**Lowest-Rated Categories (min. 50 reviews)**
| Category | Total Reviews | Avg. Review Score |
|---|---|---|
| Office Furniture | 1,687 | 3.49 |
| Fashion — Male Clothing | 131 | 3.64 |
| Fixed Telephony | 262 | 3.68 |
| Home Comfort | 435 | 3.83 |
| Audio | 361 | 3.83 |

**Payment Methods**
| Type | Total Payments | Avg. Installments |
|---|---|---|
| Credit Card | 76,795 | 3.5 |
| Boleto | 19,784 | 1.0 |
| Voucher | 5,775 | 1.0 |
| Debit Card | 1,529 | 1.0 |

Credit card is the dominant payment method and the only one where customers regularly split payments across installments (avg. 3.5) — all other methods are essentially single-payment.

**Delivery Performance**
| Status | Orders | % of Total |
|---|---|---|
| On Time | 88,652 | 91.89% |
| Late | 7,826 | 8.11% |

**Delivery Status vs. Review Score**
| Status | Avg. Review Score |
|---|---|
| On Time | 4.29 |
| Late | 2.57 |

Late deliveries are associated with a **1.72-point drop** in average review score (4.29 → 2.57) — a stark demonstration of how operational performance directly drives customer satisfaction.

## 📁 Repository Contents

- `olist_project.sql` — full script: schema creation, data import (with cleaning steps), and all analysis queries
- 9 source CSV files (Customers, Orders, Order Items, Payments, Reviews, Products, Sellers, Geolocation, Category Translation)

## 📌 Dataset Source

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle
