# DuckDB Retail Analytics

A hands-on analytics project built while learning **DuckDB, SQL, and Pandas interoperability**.

The project uses a retail transactions dataset to practice querying CSV data directly with DuckDB and answering real business analytics questions.

## 🎯 Objective

The goal of this project is to develop practical SQL analytics skills by working with a real-world retail dataset.

I focused on:

* Querying CSV files directly with DuckDB
* Understanding SQL aggregation
* Using `GROUP BY`
* Using `ORDER BY` and `LIMIT`
* Calculating business metrics
* Analyzing revenue and sales
* Working with DuckDB and Pandas together
* Translating business questions into SQL queries

---

## 🛠️ Technologies

* **Python**
* **DuckDB**
* **SQL**
* **Pandas**
* **Jupyter Notebook**

---

## 📂 Project Structure

```text
duckdb-retail-analytics/
│
├── online_retail.csv
├── prac_1.ipynb
├── prac_2.ipynb
├── prac_3.ipynb
├── DuckDB Learning Notes.docx
└── README.md
```

> File names may differ depending on the final organization of the project.

---

## 📊 Dataset

The project uses a retail transaction dataset containing information such as:

* Invoice number
* Product/stock code
* Product description
* Quantity
* Invoice date
* Unit price
* Customer ID
* Country

The dataset contains individual transaction lines, meaning one invoice can contain multiple products.

---

# 🦆 DuckDB Fundamentals

## Connecting to DuckDB

I learned how to create a DuckDB connection from Python.

```python
import duckdb

conn = duckdb.connect()
```

DuckDB can run analytical SQL directly inside the Python process without requiring a separate database server.

---

## Querying CSV Directly

One of the key concepts I learned was that DuckDB can query a CSV file directly.

```sql
SELECT *
FROM 'online_retail.csv';
```

This means the CSV does not have to be imported into PostgreSQL or converted into a database table before analysis.

---

# 🔎 Basic SQL Queries

## SELECT

Used to retrieve columns from the dataset.

```sql
SELECT
    Country,
    Quantity,
    UnitPrice
FROM retail;
```

## WHERE

Used to filter rows.

```sql
SELECT *
FROM retail
WHERE Country = 'United Kingdom';
```

## ORDER BY

Used to sort results.

```sql
SELECT *
FROM retail
ORDER BY Quantity DESC;
```

## LIMIT

Used to restrict the number of returned rows.

```sql
SELECT *
FROM retail
LIMIT 10;
```

---

# 📈 Aggregation

I practiced the major SQL aggregation functions:

```text
COUNT()
SUM()
AVG()
MIN()
MAX()
```

For example:

```sql
SELECT
    SUM(Quantity * UnitPrice) AS total_revenue
FROM retail;
```

This calculates total revenue using:

```text
Revenue = Quantity × UnitPrice
```

---

# 🪣 GROUP BY

One of the most important concepts I learned was `GROUP BY`.

My mental model:

> `GROUP BY` creates buckets. Aggregate functions calculate something inside those buckets.

For example, revenue by country:

```sql
SELECT
    Country,
    SUM(Quantity * UnitPrice) AS revenue
FROM retail
GROUP BY Country
ORDER BY revenue DESC;
```

The query creates one group/bucket for each country and calculates the total revenue inside each bucket.

---

# 💰 Revenue Analysis

## Total Revenue

```sql
SELECT
    SUM(Quantity * UnitPrice) AS total_revenue
FROM retail;
```

## Revenue by Country

```sql
SELECT
    Country,
    SUM(Quantity * UnitPrice) AS revenue
FROM retail
GROUP BY Country
ORDER BY revenue DESC;
```

## Top Products by Revenue

```sql
SELECT
    Description,
    SUM(Quantity * UnitPrice) AS revenue
FROM retail
GROUP BY Description
ORDER BY revenue DESC
LIMIT 10;
```

## Top Products by Quantity

```sql
SELECT
    Description,
    SUM(Quantity) AS units_sold
FROM retail
GROUP BY Description
ORDER BY units_sold DESC
LIMIT 10;
```

---

# 👥 Customer Analysis

## Number of Unique Customers

```sql
SELECT
    COUNT(DISTINCT CustomerID) AS unique_customers
FROM retail;
```

## Revenue per Customer

```sql
SELECT
    CustomerID,
    SUM(Quantity * UnitPrice) AS customer_revenue
FROM retail
GROUP BY CustomerID
ORDER BY customer_revenue DESC
LIMIT 10;
```

This converts the business question:

> "How much revenue did each customer generate?"

into a SQL aggregation problem.

---

# 📅 Time-Based Analysis

I also learned how to aggregate transactions by month.

```sql
SELECT
    DATE_TRUNC('month', InvoiceDate) AS month,
    SUM(Quantity * UnitPrice) AS revenue
FROM retail
GROUP BY month
ORDER BY month;
```

To find the highest-revenue month:

```sql
SELECT
    DATE_TRUNC('month', InvoiceDate) AS month,
    SUM(Quantity * UnitPrice) AS revenue
FROM retail
GROUP BY month
ORDER BY revenue DESC
LIMIT 1;
```

---

# 🐼 DuckDB + Pandas

I learned that DuckDB and Pandas are complementary rather than competing tools.

DuckDB can perform the SQL analytics:

```python
result = conn.execute("""
    SELECT
        Country,
        SUM(Quantity * UnitPrice) AS revenue
    FROM retail
    GROUP BY Country
    ORDER BY revenue DESC
""").df()
```

The `.df()` converts the query result into a Pandas DataFrame.

Pandas can then be used for Python-based DataFrame operations:

```python
result["revenue"].mean()
```

### Mental Model

```text
CSV
 ↓
DuckDB
 ↓
SQL analysis
 ↓
Pandas DataFrame
 ↓
Python/DataFrame operations
```

The important lesson is not to force one tool to do everything.

---

# 🧠 Key Concepts Learned

### 1. GROUP BY ≠ SUM

`GROUP BY` defines the groups.

`SUM()` calculates a value inside those groups.

```text
GROUP BY → "How should I divide the data?"

SUM()    → "What should I calculate for each group?"
```

### 2. COUNT(*) vs COUNT(DISTINCT)

`COUNT(*)` counts rows.

```sql
COUNT(*)
```

`COUNT(DISTINCT CustomerID)` counts unique customers.

```sql
COUNT(DISTINCT CustomerID)
```

### 3. AVG vs SUM

`AVG(UnitPrice)` calculates the average unit price.

```sql
AVG(UnitPrice)
```

Whereas:

```sql
SUM(UnitPrice)
```

calculates the total of the unit-price values.

They answer completely different questions.

### 4. LIMIT Changes the Result

For example:

```sql
LIMIT 4
```

means only four rows are returned.

If the resulting DataFrame is then averaged in Pandas, the calculation is based only on those four rows.

---

# 🧪 Business Questions Practiced

During this project I translated questions such as:

* What is the total revenue?
* Which country generated the most revenue?
* Which products generated the most revenue?
* Which products sold the most units?
* What is the average unit price?
* What is the average unit price by country?
* How many unique invoices exist?
* How many unique customers exist?
* How much revenue did each customer generate?
* Which month generated the highest revenue?

The focus was not simply writing SQL syntax, but learning how to convert a **business question → analytical operation → SQL query**.

---

# 🚧 Next Steps

The next stage of the project will expand into:

* SQL window functions
* Ranking products within groups
* More advanced analytical queries
* DuckDB inside FastAPI
* SQL query endpoints
* Natural-language-to-SQL
* Query safety
* An end-to-end NL-to-SQL analytics API

---

# 📚 Learning Notes

Detailed explanations, mistakes, experiments, and additional notes are documented in:

`DuckDB Learning Notes.docx`

---

# 📌 Status

**Current stage:** DuckDB + SQL Analytics Fundamentals

**Completed:**

* [x] DuckDB setup
* [x] Direct CSV querying
* [x] Basic SQL
* [x] Aggregations
* [x] GROUP BY
* [x] Revenue analysis
* [x] Customer analysis
* [x] Time-based analysis
* [x] DuckDB ↔ Pandas interoperability

**Next:** Advanced SQL analytics and window functions.
