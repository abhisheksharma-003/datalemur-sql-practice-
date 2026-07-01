# Laptop vs. Mobile Viewership

**Platform:** DataLemur  
**Company:** NY Times  
**Difficulty:** Easy  

---

## Problem

Calculate the total number of views for:

* **Laptop devices**
* **Mobile devices**, where mobile includes both **phones** and **tablets**

Return two columns:

* `laptop_views`
* `mobile_views`

---

## Approach

* Count all rows where the device type is `laptop`.
* Count all rows where the device type is either `phone` or `tablet`.
* Use the `FILTER` clause to perform conditional aggregation in a single query.

---

## SQL Solution

```sql
SELECT
    COUNT(*) FILTER (WHERE device_type = 'laptop') AS laptop_views,
    COUNT(*) FILTER (WHERE device_type IN ('tablet', 'phone')) AS mobile_views
FROM viewership;
```

---

# Query Explanation

## Step 1: Count Laptop Views

```sql
COUNT(*) FILTER (WHERE device_type = 'laptop')
```

Counts only the rows where the device type is **laptop**.

Example:

| device_type |
| ----------- |
| laptop      |
| phone       |
| laptop      |
| tablet      |

Result:

```text
Laptop Views = 2
```

---

## Step 2: Count Mobile Views

```sql
COUNT(*) FILTER (
    WHERE device_type IN ('tablet', 'phone')
)
```

Counts rows where the device type is either **phone** or **tablet**.

Example:

| device_type |
| ----------- |
| laptop      |
| phone       |
| tablet      |
| phone       |

Result:

```text
Mobile Views = 3
```

---

## Step 3: Return Both Counts

The query performs both aggregations in a single scan of the table.

Output:

| laptop_views | mobile_views |
| -----------: | -----------: |
|         1250 |         4876 |

---

## Alternative Approaches

### 1. Using `FILTER` *(Recommended for PostgreSQL)*

```sql
SELECT
    COUNT(*) FILTER (WHERE device_type = 'laptop') AS laptop_views,
    COUNT(*) FILTER (WHERE device_type IN ('tablet', 'phone')) AS mobile_views
FROM viewership;
```

**Database Support:** PostgreSQL

**Why use it?**

* Very concise.
* Easy to read.
* Designed specifically for conditional aggregation.

---

### 2. Using `SUM(CASE WHEN ...)`

```sql
SELECT
    SUM(CASE
            WHEN device_type = 'laptop' THEN 1
            ELSE 0
        END) AS laptop_views,
    SUM(CASE
            WHEN device_type IN ('tablet', 'phone') THEN 1
            ELSE 0
        END) AS mobile_views
FROM viewership;
```

**Database Support:** All major SQL databases

**Why use it?**

* Works across almost every SQL database.
* Most common interview approach.

---

### 3. Using `COUNT(CASE WHEN ...)`

```sql
SELECT
    COUNT(
        CASE
            WHEN device_type = 'laptop'
            THEN 1
        END
    ) AS laptop_views,

    COUNT(
        CASE
            WHEN device_type IN ('tablet', 'phone')
            THEN 1
        END
    ) AS mobile_views
FROM viewership;
```

**Database Support:** All major SQL databases

**Why use it?**

* Portable across databases.
* Another popular method for conditional counting.

---

## Understanding `FILTER`

`FILTER` applies a condition directly to an aggregate function.

Syntax:

```sql
AGGREGATE_FUNCTION(...)
FILTER (WHERE condition)
```

Example:

```sql
COUNT(*) FILTER (WHERE department = 'HR')
```

Only rows matching the condition contribute to the aggregate.

It avoids writing lengthy `CASE WHEN` expressions and improves readability.

---

## Understanding `IN`

The `IN` operator checks whether a value belongs to a list of values.

Syntax:

```sql
WHERE column_name IN (value1, value2, value3)
```

Example:

```sql
WHERE device_type IN ('tablet', 'phone')
```

Equivalent to:

```sql
WHERE device_type = 'tablet'
   OR device_type = 'phone'
```

---

## Understanding Conditional Aggregation

Conditional aggregation applies conditions while performing aggregate calculations.

Common approaches include:

### Using `FILTER`

```sql
COUNT(*) FILTER (WHERE device_type = 'laptop')
```

### Using `SUM(CASE WHEN ...)`

```sql
SUM(
    CASE
        WHEN device_type = 'laptop'
        THEN 1
        ELSE 0
    END
)
```

### Using `COUNT(CASE WHEN ...)`

```sql
COUNT(
    CASE
        WHEN device_type = 'laptop'
        THEN 1
    END
)
```

All three approaches return the same result.

---

## Concepts

* Aggregate Functions (`COUNT`)
* `FILTER`
* Conditional Aggregation
* `CASE WHEN`
* `IN`
* `SUM()`
* `COUNT()`

---

## Key Takeaways

* `FILTER` provides a clean and concise way to perform conditional aggregation in PostgreSQL.
* `SUM(CASE WHEN ...)` is the most portable solution and works across nearly all SQL databases.
* `COUNT(CASE WHEN ...)` is another common technique for conditional counting.
* The `IN` operator simplifies filtering when checking multiple values.
* Conditional aggregation allows multiple counts or calculations to be performed in a single query without scanning the table multiple times.
