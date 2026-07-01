# App Click-through Rate (CTR)

**Platform:** DataLemur 
**Company:** Facebook 
**Difficulty:** Easy 

---
## Problem

Calculate the click-through rate (CTR) for each app in 2022. CTR is defined as:

> CTR = (Number of Clicks / Number of Impressions) × 100

Return the result rounded to two decimal places.

---

## Approach

* Filter records from the year 2022.
* Group data by `app_id`.
* Count clicks and impressions separately using `CASE WHEN`.
* Calculate the CTR as `(clicks / impressions) × 100`.
* Round the result to 2 decimal places.

---

## SQL Solution

```sql
SELECT
    app_id,
    ROUND(
        100.0 *
        SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /
        SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END),
        2
    ) AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01'
  AND timestamp < '2023-01-01'
GROUP BY app_id;
```
## Alternative Approaches

### 1. Using `SUM(CASE WHEN ...)`  *(Recommended)*

**Database Support:** PostgreSQL, MySQL, SQL Server, Oracle, SQLite

```sql
SELECT
    app_id,
    ROUND(
        100.0 *
        SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /
        SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END),
        2
    ) AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01'
  AND timestamp < '2023-01-01'
GROUP BY app_id;
```

**Why use it?**

* Portable across almost all SQL databases.
* Most common approach in SQL interviews.
* Easy to understand and maintain.

---

### 2. Using `COUNT(CASE WHEN ...)`

**Database Support:** PostgreSQL, MySQL, SQL Server, Oracle, SQLite

```sql
SELECT
    app_id,
    ROUND(
        100.0 *
        COUNT(CASE WHEN event_type = 'click' THEN 1 END) /
        COUNT(CASE WHEN event_type = 'impression' THEN 1 END),
        2
    ) AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01'
  AND timestamp < '2023-01-01'
GROUP BY app_id;
```

**Why use it?**

* Provides a concise way to count conditional rows.
* Produces the same result as `SUM(CASE WHEN ...)`.
* Widely supported across major SQL databases.

---

### 3. Using `COUNT(*) FILTER (WHERE ...)`

**Database Support:** PostgreSQL

```sql
SELECT
    app_id,
    ROUND(
        100.0 *
        COUNT(*) FILTER (WHERE event_type = 'click') /
        COUNT(*) FILTER (WHERE event_type = 'impression'),
        2
    ) AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01'
  AND timestamp < '2023-01-01'
GROUP BY app_id;
```

**Why use it?**

* Cleaner and more readable than `CASE WHEN`.
* Preferred PostgreSQL syntax for conditional aggregation.
* Ideal when multiple conditional aggregates are used in the same query.

> **Note:** The `FILTER` clause is PostgreSQL-specific and is not supported in databases such as MySQL. For maximum compatibility, use `SUM(CASE WHEN ...)` or `COUNT(CASE WHEN ...)`.

---

## Concepts Practiced

* Conditional Aggregation
* CASE WHEN
* SUM()
* GROUP BY
* Date Filtering
* ROUND()
