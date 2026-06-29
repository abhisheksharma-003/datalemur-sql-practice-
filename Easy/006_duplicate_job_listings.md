# Duplicate Job Listings

**Platform:** DataLemur
**Company:** LinkedIn
**Difficulty:** Easy 
**Concepts:** CTE, COUNT(), GROUP BY, COUNT(DISTINCT), HAVING, WHERE

---

## Problem

Find the number of companies that have posted **duplicate job listings**.

A duplicate job listing is defined as two or more job postings within the **same company** that have the **same title** and **same description**.

Return the total number of companies with duplicate job listings.

---

## Approach

This problem requires two levels of aggregation:

1. Group job listings by **company**, **title**, and **description**.
2. Count how many times each unique job listing appears.
3. Keep only duplicate listings.
4. Count the distinct companies that have at least one duplicate listing.

---

## SQL Solution

```sql
WITH job_count_cte AS (
    SELECT
        company_id,
        title,
        description,
        COUNT(job_id) AS job_count
    FROM job_listings
    GROUP BY company_id, title, description
)

SELECT
    COUNT(DISTINCT company_id) AS duplicate_companies
FROM job_count_cte
WHERE job_count > 1;
```

---

# Query Explanation

## Step 1: Group Similar Job Listings

```sql
SELECT
    company_id,
    title,
    description,
    COUNT(job_id) AS job_count
FROM job_listings
GROUP BY company_id, title, description;
```

Job listings are grouped by:

* Company
* Job Title
* Job Description

Example:

| company_id | title             | description  | job_count |
| ---------- | ----------------- | ------------ | --------: |
| 101        | Data Analyst      | SQL & Python |         2 |
| 101        | Data Engineer     | ETL Pipeline |         1 |
| 102        | Software Engineer | Backend      |         3 |

Each row now represents one unique job listing.

---

## Step 2: Create the CTE

```sql
WITH job_count_cte AS (
    ...
)
```

The grouped result is stored in a **Common Table Expression (CTE)** named `job_count_cte`.

Temporary Result:

| company_id | title             | description  | job_count |
| ---------- | ----------------- | ------------ | --------: |
| 101        | Data Analyst      | SQL & Python |         2 |
| 101        | Data Engineer     | ETL Pipeline |         1 |
| 102        | Software Engineer | Backend      |         3 |

The CTE behaves like a temporary table during query execution.

---

## Step 3: Keep Only Duplicate Listings

```sql
WHERE job_count > 1
```

Only job listings appearing more than once are considered duplicates.

Example:

| company_id | job_count | Included |
| ---------- | --------: | :------: |
| 101        |         2 |     ✅    |
| 101        |         1 |     ❌    |
| 102        |         3 |     ✅    |

---

## Step 4: Count Companies

```sql
COUNT(DISTINCT company_id)
```

Multiple duplicate listings from the same company should only be counted once.

Example:

| company_id |
| ---------- |
| 101        |
| 101        |
| 102        |

Result:

```text
2
```

---

## Alternative Approaches

### 1. Using a Common Table Expression (CTE)  *(Recommended)*

```sql
WITH job_count_cte AS (
    SELECT
        company_id,
        title,
        description,
        COUNT(job_id) AS job_count
    FROM job_listings
    GROUP BY company_id, title, description
)

SELECT
    COUNT(DISTINCT company_id) AS duplicate_companies
FROM job_count_cte
WHERE job_count > 1;
```

**Database Support:** PostgreSQL, MySQL 8+, SQL Server, Oracle

**Why use it?**

* Breaks the query into logical steps.
* Easy to read and debug.
* Preferred for analytical SQL queries.

---

### 2. Using a Derived Table

```sql
SELECT
    COUNT(DISTINCT company_id) AS duplicate_companies
FROM (
    SELECT
        company_id,
        title,
        description,
        COUNT(job_id) AS job_count
    FROM job_listings
    GROUP BY company_id, title, description
) AS duplicate_jobs
WHERE job_count > 1;
```

**Database Support:** All major SQL databases

**Why use it?**

* Produces the same result as the CTE.
* Standard SQL syntax.
* Common in SQL interviews.

---

## Understanding `COUNT(DISTINCT)`

```sql
COUNT(DISTINCT company_id)
```

`COUNT(DISTINCT)` counts only **unique** values.

Example:

| company_id |
| ---------- |
| 101        |
| 101        |
| 102        |
| 103        |
| 103        |

Result:

```text
COUNT(company_id)          = 5
COUNT(DISTINCT company_id) = 3
```

Without `DISTINCT`, companies with multiple duplicate listings would be counted multiple times.

---

## Understanding Multiple `GROUP BY` Columns

```sql
GROUP BY
    company_id,
    title,
    description
```

SQL groups rows using the **combination** of all listed columns.

Example:

| company_id | title        | description |
| ---------- | ------------ | ----------- |
| 101        | Data Analyst | SQL         |
| 101        | Data Analyst | SQL         |
| 101        | Data Analyst | Python      |

Result:

| company_id | title        | description | Count |
| ---------- | ------------ | ----------- | ----: |
| 101        | Data Analyst | SQL         |     2 |
| 101        | Data Analyst | Python      |     1 |

Rows are grouped only when **all grouped columns match**.

---

## Understanding the CTE

A **Common Table Expression (CTE)** is a temporary named result set created using the `WITH` clause.

```sql
WITH job_count_cte AS (
    ...
)
```

The CTE is executed first and its result becomes a temporary table that the main query can reference.

Execution Flow:

```
Job Listings
      │
      ▼
GROUP BY
(company_id, title, description)
      │
      ▼
Count Duplicate Listings
      │
      ▼
Store Result in CTE
      │
      ▼
Filter job_count > 1
      │
      ▼
COUNT(DISTINCT company_id)
```

The CTE exists only while the query is executing.

---

## Concepts

* Common Table Expressions (CTEs)
* Derived Tables
* Aggregate Functions (`COUNT`)
* `COUNT(DISTINCT)`
* Multiple Column `GROUP BY`
* Multi-Level Aggregation
* Filtering (`WHERE`)

---

## Key Takeaways

* Multi-level aggregation is useful when one aggregation depends on the result of another.
* Grouping by multiple columns creates groups based on the combination of all specified columns.
* `COUNT(DISTINCT)` counts unique values and prevents duplicate counting.
* CTEs improve readability by separating complex logic into smaller, logical steps.
* A CTE behaves like a temporary table and exists only during query execution.
* A derived table provides the same functionality and is often used for shorter queries.
* Breaking analytical problems into intermediate steps makes SQL queries easier to understand, debug, and maintain.
