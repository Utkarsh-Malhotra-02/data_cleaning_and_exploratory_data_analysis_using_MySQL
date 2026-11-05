<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Data Cleaning & Exploratory Data Analysis</title>
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
        ul {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📊 Data Cleaning & Exploratory Data Analysis (EDA) using MySQL</h1>
        <p>Welcome to the <strong>Data Cleaning & Exploratory Data Analysis (EDA)</strong> project repository! This project showcases a complete end-to-end data processing workflow using MySQL, covering raw data standardization, handling missing values, duplicate removal, schema updates, and analytical querying to extract business insights.</p>

        <hr>

        <h2>🛠️ Project Architecture & Workflow</h2>
        <ul>
            <li><strong>Database Setup:</strong> Creating a staging database and table structure to preserve raw input data.</li>
            <li><strong>Data Cleaning & Preprocessing:</strong> Removing duplicates, standardizing text fields, fixing dates, and handling missing values.</li>
            <li><strong>Exploratory Data Analysis (EDA):</strong> Running summary statistics, group aggregations, rolling totals, and ranking queries using window functions.</li>
        </ul>

        <hr>

        <h2>💻 Technical Stack & Tools</h2>
        <ul>
            <li><strong>Database Engine:</strong> MySQL Server / MySQL Workbench</li>
            <li><strong>Language:</strong> SQL (DDL & DML)</li>
            <li><strong>Key Concepts:</strong> CTEs, Window Functions (<code>ROW_NUMBER()</code>, <code>DENSE_RANK()</code>, <code>SUM() OVER()</code>), String Functions, Date Formatting.</li>
        </ul>

        <hr>

        <h2>📋 Data Cleaning Steps</h2>

        <h3>1. Removing Duplicates</h3>
        <p>Duplicate records were identified and removed using window functions over unique partition keys:</p>
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
        <p>Text formatting and date types were unified across records:</p>
<pre><code>-- Trimming extra whitespace from company names
UPDATE staging_table
SET company = TRIM(company);

-- Converting date text columns into proper SQL DATE format
UPDATE staging_table
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE staging_table
MODIFY COLUMN `date` DATE;</code></pre>

        <h3>3. Handling Null & Blank Values</h3>
        <p>Missing strings were converted to actual database <code>NULL</code> values and populated using matching records where possible:</p>
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
        <p>Rows lacking essential metric values and helper columns created during processing were cleaned up:</p>
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
            <li><strong>High-Impact Periods:</strong> Pinpointed peak layoff windows across major tech and financial cycles.</li>
            <li><strong>Industry Vulnerability:</strong> Evaluated which sectors experienced the highest volume of reductions.</li>
            <li><strong>Funding Stage Impact:</strong> Analyzed layoff trends relative to company maturity (e.g., Post-IPO vs. Early Stage).</li>
        </ul>

        <hr>

        <h2>🚀 How to Run This Project</h2>
        <ol>
            <li>Clone the repository locally: <code>git clone https://github.com/Utkarsh-Malhotra-02/data_cleaning_and_exploratory_data_analysis_using_MySQL.git</code></li>
            <li>Open MySQL Workbench and create your local database instance.</li>
            <li>Execute <code>layoffs_data_cleaning.sql</code> followed by <code>layoffs_eda.sql</code>.</li>
        </ol>

        <hr>
        <p><em>Author: Utkarsh Malhotra | GitHub: <a href="https://github.com/Utkarsh-Malhotra-02" target="_blank">@Utkarsh-Malhotra-02</a></em></p>
    </div>
</body>
</html>
