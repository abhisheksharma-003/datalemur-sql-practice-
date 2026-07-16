# IBM db2 Product Analytics

**Platform:** DataLemur  
**Company:** IBM  
**Difficulty:** Easy  

---

## Problem

IBM wants to analyze how employees are utilizing the **Db2 database** during the **third quarter of 2023 (July–September)**.

For every employee:

- Count the number of **unique SQL queries** they executed during Q3 2023.
- Employees who **did not execute any queries** should still be included.
- Build a histogram showing:
  - `unique_queries`
  - `employee_count`

---

## Approach

- Start from the `employees` table to include every employee.
- Perform a `LEFT JOIN` with the `queries` table.
- Filter only Q3 2023 queries inside the `JOIN` condition.
- Count distinct `query_id` values for each employee.
- Replace missing counts with `0` using `COALESCE()`.
- Group employees by their unique query count to generate the histogram.

---

## SQL Solution

```sql
WITH employee_queries AS (
    SELECT
        e.employee_id,
        COALESCE(COUNT(DISTINCT q.query_id), 0) AS unique_queries
    FROM employees AS e
    LEFT JOIN queries AS q
        ON e.employee_id = q.employee_id
       AND query_starttime >= '2023-07-01T00:00:00Z'
       AND query_starttime < '2023-10-01T00:00:00Z'
    GROUP BY e.employee_id
)

SELECT
    unique_queries,
    COUNT(employee_id) AS employee_count
FROM employee_queries
GROUP BY unique_queries
ORDER BY unique_queries;
```

---

# Query Explanation

## Step 1: Include Every Employee

```sql
FROM employees e
LEFT JOIN queries q
```

The query begins with the `employees` table.

Using a `LEFT JOIN` guarantees that employees who did not execute any queries are still included.

Example:

| employee_id | query_id |
|------------:|---------:|
|1|101|
|2|205|
|3|NULL|

Employee **3** executed no queries but still appears in the result.

---

## Step 2: Filter Only Q3 Queries

```sql
AND query_starttime >= '2023-07-01'
AND query_starttime < '2023-10-01'
```

The filter keeps only queries executed between:

- July 1, 2023
- September 30, 2023

Using `< '2023-10-01'` includes every timestamp on September 30.

---

### Why is the Date Filter Inside the `JOIN` Instead of the `WHERE` Clause?

When using a `LEFT JOIN`, placing conditions on the right table inside the `JOIN` preserves all rows from the left table.

In this problem, we want to include employees who **did not execute any queries during Q3 2023**. Therefore, the date filter must be part of the join condition.

### Correct

```sql
FROM employees e
LEFT JOIN queries q
    ON e.employee_id = q.employee_id
   AND q.query_starttime >= '2023-07-01'
   AND q.query_starttime < '2023-10-01'
```

**Why?**

- Every employee is returned.
- Only queries from Q3 2023 are matched.
- Employees with no Q3 queries remain in the result with `NULL` values, which are later converted to `0`.

---

### Incorrect

```sql
FROM employees e
LEFT JOIN queries q
    ON e.employee_id = q.employee_id

WHERE q.query_starttime >= '2023-07-01'
  AND q.query_starttime < '2023-10-01'
```

**Why is this wrong?**

The `WHERE` clause is applied **after** the join is completed.

For employees with no matching queries, `q.query_starttime` is `NULL`.

The condition:

```sql
q.query_starttime >= '2023-07-01'
```

evaluates to **FALSE** for `NULL` values, so those employees are removed from the result.

As a result, the `LEFT JOIN` effectively behaves like an **INNER JOIN**, which violates the problem requirement.

---

### Example

**Employees**

| employee_id |
|------------:|
|1|
|2|
|3|

**Queries**

| employee_id | query_starttime |
|------------:|-----------------|
|1|2023-08-10|
|2|2023-06-15|

Employee **3** has no queries.

#### Using the Date Filter Inside the `JOIN`

| employee_id | query_starttime |
|------------:|-----------------|
|1|2023-08-10|
|2|NULL|
|3|NULL|

All employees are preserved.

#### Using the Date Filter in the `WHERE` Clause

| employee_id | query_starttime |
|------------:|-----------------|
|1|2023-08-10|

Employees **2** and **3** disappear because their `query_starttime` values are `NULL` after the join.

---

### Rule of Thumb

- Use conditions in the **`JOIN`** when filtering rows from the joined table while still preserving unmatched rows.
- Use the **`WHERE`** clause when you want to filter the final result set after the join.

This is one of the most frequently tested SQL interview concepts involving `LEFT JOIN`.

---

## Step 3: Count Unique Queries

```sql
COUNT(DISTINCT q.query_id)
```

Counts only distinct queries executed by each employee.

Example:

| employee_id | query_id |
|------------:|---------:|
|1|100|
|1|100|
|1|105|

Result:

```
COUNT(query_id) = 3

COUNT(DISTINCT query_id) = 2
```

Only unique query IDs are counted.

---

## Step 4: Replace NULL with Zero

```sql
COALESCE(COUNT(DISTINCT q.query_id), 0)
```

Employees with no matching queries receive:

```
NULL
```

`COALESCE()` converts it into:

```
0
```

Example:

| employee_id | unique_queries |
|------------:|---------------:|
|1|5|
|2|2|
|3|0|

---

## Step 5: Create the Histogram

```sql
GROUP BY unique_queries
```

Employees having the same number of unique queries are grouped together.

Example:

| employee_id | unique_queries |
|------------:|---------------:|
|1|2|
|2|1|
|3|2|
|4|0|

Histogram:

| unique_queries | employee_count |
|---------------:|---------------:|
|0|1|
|1|1|
|2|2|

---

## Step 6: Sort the Result

```sql
ORDER BY unique_queries;
```

Displays the histogram in ascending order of unique query count.

---

## Alternative Approaches

### 1. Using a CTE *(Recommended)*

```sql
WITH employee_queries AS (
    SELECT
        e.employee_id,
        COALESCE(COUNT(DISTINCT q.query_id),0) AS unique_queries
    FROM employees e
    LEFT JOIN queries q
        ON e.employee_id=q.employee_id
       AND query_starttime >= '2023-07-01'
       AND query_starttime < '2023-10-01'
    GROUP BY e.employee_id
)

SELECT
    unique_queries,
    COUNT(employee_id) AS employee_count
FROM employee_queries
GROUP BY unique_queries;
```

**Database Support:** All major SQL databases

**Why use it?**

- Easy to understand.
- Separates the problem into two logical steps.
- Most common interview solution.

---

### 2. Using a Derived Table

```sql
SELECT
    unique_queries,
    COUNT(*) AS employee_count
FROM (
    SELECT
        e.employee_id,
        COUNT(DISTINCT q.query_id) AS unique_queries
    FROM employees e
    LEFT JOIN queries q
        ON e.employee_id=q.employee_id
       AND query_starttime >= '2023-07-01'
       AND query_starttime < '2023-10-01'
    GROUP BY e.employee_id
) t
GROUP BY unique_queries;
```

**Database Support:** All SQL databases

**Why use it?**

- Produces the same result without using a CTE.
- Useful in databases where CTEs are discouraged.

---

## Understanding `LEFT JOIN`

`LEFT JOIN` returns every row from the left table.

If no matching row exists in the right table, SQL fills the columns with `NULL`.

Example:

| Employee | Query |
|---------:|------:|
|1|101|
|2|205|
|3|NULL|

Without `LEFT JOIN`, Employee **3** would disappear.

---

## Understanding `COUNT(DISTINCT)`

`COUNT(DISTINCT column)` counts only unique values.

Example:

| query_id |
|---------:|
|10|
|10|
|15|

Result:

```
COUNT(query_id) = 3

COUNT(DISTINCT query_id) = 2
```

---

## Understanding `COALESCE()`

`COALESCE()` returns the first non-NULL value.

Syntax:

```sql
COALESCE(value, replacement)
```

Example:

```sql
COALESCE(NULL, 0)
```

Result:

```
0
```

---

## Understanding Common Table Expressions (CTEs)

A CTE creates a temporary result set that can be referenced later.

Syntax:

```sql
WITH cte_name AS (
    SELECT ...
)
SELECT ...
FROM cte_name;
```

Benefits:

- Improves readability.
- Breaks large problems into smaller steps.
- Makes debugging easier.

---

## Understanding `GROUP BY`

`GROUP BY` groups rows having the same value.

Example:

Before grouping:

| unique_queries |
|---------------:|
|2|
|2|
|1|
|0|

After grouping:

- 0
- 1
- 2

The aggregate function (`COUNT`) is then applied to each group.

---

## Concepts

- Common Table Expressions (CTEs)
- LEFT JOIN
- COUNT(DISTINCT)
- COALESCE
- GROUP BY
- Date Filtering
- Aggregate Functions
- Histogram Generation

---

## Key Takeaways

- Use `LEFT JOIN` when rows with no matching records must be preserved.
- Apply date filters inside the `JOIN` condition to avoid removing unmatched employees.
- `COUNT(DISTINCT)` counts only unique values.
- `COALESCE()` replaces `NULL` values with meaningful defaults.
- Complex aggregation problems become easier by solving them in stages using a CTE.
- Histogram questions typically involve two aggregations: first per entity, then by frequency.