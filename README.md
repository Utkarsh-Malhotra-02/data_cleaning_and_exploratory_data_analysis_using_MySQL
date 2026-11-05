# 📊 Data Cleaning & Exploratory Data Analysis (EDA) using MySQL

Welcome to the **Data Cleaning & Exploratory Data Analysis (EDA)** project repository! This project showcases a complete end-to-end data processing workflow using MySQL, covering raw data standardization, handling missing values, duplicate removal, schema updates, and analytical querying to extract business insights.

---

## 🛠️ Project Architecture & Workflow

1. **Database Setup**: Creating a staging database and table structure to preserve raw input data.
2. **Data Cleaning & Preprocessing**:
   - Removing duplicate records using `ROW_NUMBER()` and CTEs / Window Functions.
   - Standardizing text fields (trimming whitespace, unifying naming conventions).
   - Standardizing dates and altering column data types (`STR_TO_DATE`, `ALTER TABLE`).
   - Handling `NULL` values and blank string entries appropriately.
   - Removing irrelevant or redundant columns/rows.
3. **Exploratory Data Analysis (EDA)**:
   - Summary statistics and distributions.
   - Aggregations by group (e.g., industry, country, year, company).
   - Rolling totals and ranking using Window Functions (`SUM() OVER()`, `DENSE_RANK()`).

---

## 💻 Technical Stack & Tools

- **Database Engine**: MySQL Server / MySQL Workbench
- **Language**: SQL (Data Definition Language & Data Manipulation Language)
- **Key SQL Concepts Used**:
  - `CTE` (Common Table Expressions)
  - Window Functions (`ROW_NUMBER()`, `DENSE_RANK()`, `SUM() OVER()`)
  - String Functions (`TRIM()`, `LOWER()`, `LIKE`)
  - Date Functions (`STR_TO_DATE()`, `DATE_FORMAT()`)
  - Control Flow (`CASE WHEN`, `COALESCE()`)

---

## 📋 Data Cleaning Steps Summary

### 1. Removing Duplicates
Duplicate records were identified using window functions over unique partition keys:
```sql
WITH duplicate_cte AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
           ) AS row_num
    FROM staging_table
)
DELETE FROM staging_table
WHERE row_num > 1;
