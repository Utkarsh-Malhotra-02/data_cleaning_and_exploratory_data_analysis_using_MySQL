```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Data Cleaning & Exploratory Data Analysis using MySQL</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            line-height: 1.6; 
            margin: 0; 
            padding: 20px; 
            background-color: #f4f4f9; 
            color: #333; 
        } 
        .container { 
            max-width: 900px; 
            margin: auto; 
            background: #fff; 
            padding: 30px; 
            border-radius: 8px; 
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); 
        } 
        h1, h2, h3 { 
            color: #2c3e50; 
        } 
        pre { 
            background: #272822; 
            color: #f8f8f2; 
            padding: 15px; 
            border-radius: 5px; 
            overflow-x: auto; 
        } 
        code { 
            font-family: Consolas, Monaco, monospace; 
        } 
        ul, ol { 
            margin-bottom: 20px; 
        } 
        hr { 
            border: 0; 
            height: 1px; 
            background: #ddd; 
            margin: 20px 0; 
        }
    </style>
</head>
<body>
<div class="container">
    <h1>📊 Data Cleaning & Exploratory Data Analysis (EDA) using MySQL</h1>
    <p>Welcome to the Data Cleaning & Exploratory Data Analysis (EDA) project repository! This project showcases a complete end-to-end data processing workflow using MySQL, covering raw data standardization, handling missing values, duplicate removal, schema updates, and analytical querying to extract business insights.</p>
    
    <hr>

    <h2>🛠️ Project Architecture & Workflow</h2>
    <ol>
        <li><strong>Database Setup:</strong> Creating a staging database and table structure to preserve raw input data.</li>
        <li><strong>Data Cleaning & Preprocessing:</strong>
            <ul>
                <li>Removing duplicate records using <code>ROW_NUMBER()</code> and CTEs / Window Functions.</li>
                <li>Standardizing text fields (trimming whitespace, unifying naming conventions).</li>
                <li>Standardizing dates and altering column data types (<code>STR_TO_DATE</code>, <code>ALTER TABLE</code>).</li>
                <li>Handling <code>NULL</code> values and blank string entries appropriately.</li>
                <li>Removing irrelevant or redundant columns/rows.</li>
            </ul>
        </li>
        <li><strong>Exploratory Data Analysis (EDA):</strong>
            <ul>
                <li>Summary statistics and distributions.</li>
                <li>Aggregations by group (e.g., industry, country, year, company).</li>
                <li>Rolling totals and ranking using Window Functions (<code>SUM() OVER()</code>, <code>DENSE_RANK()</code>).</li>
            </ul>
        </li>
    </ol>

    <hr>

    <h2>💻 Technical Stack & Tools</h2>
    <ul>
        <li><strong>Database Engine:</strong> MySQL Server / MySQL Workbench</li>
        <li><strong>Language:</strong> SQL (Data Definition Language & Data Manipulation Language)</li>
        <li><strong>Key SQL Concepts Used:</strong>
            <ul>
                <li><code>CTE</code> (Common Table Expressions)</li>
                <li>Window Functions (<code>ROW_NUMBER()</code>, <code>DENSE_RANK()</code>, <code>SUM() OVER()</code>)</li>
                <li>String Functions (<code>TRIM()</code>, <code>LOWER()</code>, <code>LIKE</code>)</li>
                <li>Date Functions (<code>STR_TO_DATE()</code>, <code>DATE_FORMAT()</code>)</li>
                <li>Control Flow (<code>CASE WHEN</code>, <code>COALESCE()</code>)</li>
            </ul>
        </li>
    </ul>

    <hr>

    <h2>📋 Data Cleaning Steps Summary</h2>

    <h3>1. Removing Duplicates</h3>
    <p>Duplicate records were identified using window functions over unique partition keys:</p>
    <pre><code>WITH duplicate_cte AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
           ) AS row_num
    FROM staging_table
)
DELETE FROM staging_table
WHERE row_num &gt; 1;</code></pre>

    <h3>2. Standardizing Data</h3>
    <p>Text formatting, whitespace issues, and date columns were cleaned and formatted uniformly across the dataset:</p>
    <pre><code>-- Trimming extra whitespace from company names
UPDATE staging_table
SET company = TRIM(company);

-- Converting date text columns into proper SQL DATE format
UPDATE staging_table
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE staging_table
MODIFY COLUMN `date` DATE;</code></pre>

    <h3>3. Handling Null & Blank Values</h3>
    <p>Missing or blank entries were standardized to <code>NULL</code> values, and relational data was used to populate missing rows where possible:</p>
    <pre><code>-- Replacing empty string values with NULL
UPDATE staging_table
SET industry = NULL
WHERE industry = '' OR industry = 'NULL';

-- Populating missing industry data using existing records from the same company
UPDATE staging_table t1
JOIN staging_table t2
    ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL 
  AND t2.industry IS NOT NULL;</code></pre>

    <h3>4. Removing Unnecessary Columns & Rows</h3>
    <p>Rows lacking critical metrics and helper columns utilized during processing were cleaned up:</p>
    <pre><code>-- Removing rows where both key layoff metrics are missing
DELETE FROM staging_table
WHERE total_laid_off IS NULL 
  AND percentage_laid_off IS NULL;

-- Dropping temporary processing columns like row_num if created inside the base table
ALTER TABLE staging_table
DROP COLUMN row_num;</code></pre>

    <hr>

    <h2>📈 Key Insights from EDA</h2>
    <ul>
        <li><strong>High-Impact Periods:</strong> Identified top quarters and years with maximum layoffs across companies.</li>
        <li><strong>Industry Breakdown:</strong> Highlighted top impacted sectors (e.g., Tech, Consumer, Retail).</li>
        <li><strong>Geographic & Stage Trends:</strong> Discovered layoff patterns across different countries and company funding stages (e.g., Post-IPO vs. Series A–D).</li>
        <li><strong>Rolling Trends:</strong> Calculated cumulative totals over time to visualize trajectory trends.</li>
    </ul>

    <hr>

    <h2>🚀 How to Run This Project</h2>
    <ol>
        <li><strong>Clone the Repository:</strong>
            <pre><code>git clone https://github.com/Utkarsh-Malhotra-02/data_cleaning_and_exploratory_data_analysis_using_MySQL.git
cd data_cleaning_and_exploratory_data_analysis_using_MySQL</code></pre>
        </li>
        <li><strong>Database Setup:</strong>
            <ul>
                <li>Open MySQL Workbench or your preferred SQL client.</li>
                <li>Create a database:
                    <pre><code>CREATE DATABASE IF NOT EXISTS world_layoffs;
USE world_layoffs;</code></pre>
                </li>
            </ul>
        </li>
        <li><strong>Execute SQL Scripts:</strong>
            <ul>
                <li>Run the data cleaning script: <code>layoffs_data_cleaning.sql</code></li>
                <li>Run the exploratory analysis script: <code>layoffs_eda.sql</code></li>
            </ul>
        </li>
    </ol>

    <hr>

    <h2>👤 Author</h2>
    <p><strong>Utkarsh Malhotra</strong><br>
    GitHub: <a href="https://github.com/Utkarsh-Malhotra-02" target="_blank">@Utkarsh-Malhotra-02</a></p>
    <p><em>Feel free to star ⭐️ this repository if you find it helpful!</em></p>
</div>
</body>
</html>

```The issue is happening because the SQL code blocks are missing the `<pre><code>` and `</pre></code>` wrapper tags in that snippet, causing the browser or viewer to read them as plain text instead of formatting them inside code boxes.

Here is the fully corrected, well-formed HTML code with all the code blocks properly wrapped:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Data Cleaning & Exploratory Data Analysis using MySQL</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            line-height: 1.6; 
            margin: 0; 
            padding: 20px; 
            background-color: #f4f4f9; 
            color: #333; 
        } 
        .container { 
            max-width: 900px; 
            margin: auto; 
            background: #fff; 
            padding: 30px; 
            border-radius: 8px; 
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); 
        } 
        h1, h2, h3 { 
            color: #2c3e50; 
        } 
        pre { 
            background: #272822; 
            color: #f8f8f2; 
            padding: 15px; 
            border-radius: 5px; 
            overflow-x: auto; 
        } 
        code { 
            font-family: Consolas, Monaco, monospace; 
        } 
        ul, ol { 
            margin-bottom: 20px; 
        } 
        hr { 
            border: 0; 
            height: 1px; 
            background: #ddd; 
            margin: 20px 0; 
        }
    </style>
</head>
<body>
<div class="container">
    <h1>📊 Data Cleaning & Exploratory Data Analysis (EDA) using MySQL</h1>
    <p>Welcome to the Data Cleaning & Exploratory Data Analysis (EDA) project repository! This project showcases a complete end-to-end data processing workflow using MySQL, covering raw data standardization, handling missing values, duplicate removal, schema updates, and analytical querying to extract business insights.</p>
    
    <hr>

    <h2>🛠️ Project Architecture & Workflow</h2>
    <ol>
        <li><strong>Database Setup:</strong> Creating a staging database and table structure to preserve raw input data.</li>
        <li><strong>Data Cleaning & Preprocessing:</strong>
            <ul>
                <li>Removing duplicate records using <code>ROW_NUMBER()</code> and CTEs / Window Functions.</li>
                <li>Standardizing text fields (trimming whitespace, unifying naming conventions).</li>
                <li>Standardizing dates and altering column data types (<code>STR_TO_DATE</code>, <code>ALTER TABLE</code>).</li>
                <li>Handling <code>NULL</code> values and blank string entries appropriately.</li>
                <li>Removing irrelevant or redundant columns/rows.</li>
            </ul>
        </li>
        <li><strong>Exploratory Data Analysis (EDA):</strong>
            <ul>
                <li>Summary statistics and distributions.</li>
                <li>Aggregations by group (e.g., industry, country, year, company).</li>
                <li>Rolling totals and ranking using Window Functions (<code>SUM() OVER()</code>, <code>DENSE_RANK()</code>).</li>
            </ul>
        </li>
    </ol>

    <hr>

    <h2>💻 Technical Stack & Tools</h2>
    <ul>
        <li><strong>Database Engine:</strong> MySQL Server / MySQL Workbench</li>
        <li><strong>Language:</strong> SQL (Data Definition Language & Data Manipulation Language)</li>
        <li><strong>Key SQL Concepts Used:</strong>
            <ul>
                <li><code>CTE</code> (Common Table Expressions)</li>
                <li>Window Functions (<code>ROW_NUMBER()</code>, <code>DENSE_RANK()</code>, <code>SUM() OVER()</code>)</li>
                <li>String Functions (<code>TRIM()</code>, <code>LOWER()</code>, <code>LIKE</code>)</li>
                <li>Date Functions (<code>STR_TO_DATE()</code>, <code>DATE_FORMAT()</code>)</li>
                <li>Control Flow (<code>CASE WHEN</code>, <code>COALESCE()</code>)</li>
            </ul>
        </li>
    </ul>

    <hr>

    <h2>📋 Data Cleaning Steps Summary</h2>

    <h3>1. Removing Duplicates</h3>
    <p>Duplicate records were identified using window functions over unique partition keys:</p>
    <pre><code>WITH duplicate_cte AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
           ) AS row_num
    FROM staging_table
)
DELETE FROM staging_table
WHERE row_num &gt; 1;</code></pre>

    <h3>2. Standardizing Data</h3>
    <p>Text formatting, whitespace issues, and date columns were cleaned and formatted uniformly across the dataset:</p>
    <pre><code>-- Trimming extra whitespace from company names
UPDATE staging_table
SET company = TRIM(company);

-- Converting date text columns into proper SQL DATE format
UPDATE staging_table
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE staging_table
MODIFY COLUMN `date` DATE;</code></pre>

    <h3>3. Handling Null & Blank Values</h3>
    <p>Missing or blank entries were standardized to <code>NULL</code> values, and relational data was used to populate missing rows where possible:</p>
    <pre><code>-- Replacing empty string values with NULL
UPDATE staging_table
SET industry = NULL
WHERE industry = '' OR industry = 'NULL';

-- Populating missing industry data using existing records from the same company
UPDATE staging_table t1
JOIN staging_table t2
    ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL 
  AND t2.industry IS NOT NULL;</code></pre>

    <h3>4. Removing Unnecessary Columns & Rows</h3>
    <p>Rows lacking critical metrics and helper columns utilized during processing were cleaned up:</p>
    <pre><code>-- Removing rows where both key layoff metrics are missing
DELETE FROM staging_table
WHERE total_laid_off IS NULL 
  AND percentage_laid_off IS NULL;

-- Dropping temporary processing columns like row_num if created inside the base table
ALTER TABLE staging_table
DROP COLUMN row_num;</code></pre>

    <hr>

    <h2>📈 Key Insights from EDA</h2>
    <ul>
        <li><strong>High-Impact Periods:</strong> Identified top quarters and years with maximum layoffs across companies.</li>
        <li><strong>Industry Breakdown:</strong> Highlighted top impacted sectors (e.g., Tech, Consumer, Retail).</li>
        <li><strong>Geographic & Stage Trends:</strong> Discovered layoff patterns across different countries and company funding stages (e.g., Post-IPO vs. Series A–D).</li>
        <li><strong>Rolling Trends:</strong> Calculated cumulative totals over time to visualize trajectory trends.</li>
    </ul>

    <hr>

    <h2>🚀 How to Run This Project</h2>
    <ol>
        <li><strong>Clone the Repository:</strong>
            <pre><code>git clone https://github.com/Utkarsh-Malhotra-02/data_cleaning_and_exploratory_data_analysis_using_MySQL.git
cd data_cleaning_and_exploratory_data_analysis_using_MySQL</code></pre>
        </li>
        <li><strong>Database Setup:</strong>
            <ul>
                <li>Open MySQL Workbench or your preferred SQL client.</li>
                <li>Create a database:
                    <pre><code>CREATE DATABASE IF NOT EXISTS world_layoffs;
USE world_layoffs;</code></pre>
                </li>
            </ul>
        </li>
        <li><strong>Execute SQL Scripts:</strong>
            <ul>
                <li>Run the data cleaning script: <code>layoffs_data_cleaning.sql</code></li>
                <li>Run the exploratory analysis script: <code>layoffs_eda.sql</code></li>
            </ul>
        </li>
    </ol>

    <hr>

    <h2>👤 Author</h2>
    <p><strong>Utkarsh Malhotra</strong><br>
    GitHub: <a href="https://github.com/Utkarsh-Malhotra-02" target="_blank">@Utkarsh-Malhotra-02</a></p>
    <p><em>Feel free to star ⭐️ this repository if you find it helpful!</em></p>
</div>
</body>
</html>

```
