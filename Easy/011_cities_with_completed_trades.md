# Cities With Completed Trades

**Platform:** DataLemur 
**Company:** Robinhood 
**Difficulty:** Easy 

---

## Problem

Find the **top three cities** with the highest number of **completed trade orders**.

Return:

* `city`
* `total_orders`

Sort the results by the number of completed orders in **descending order**.

---

## Approach

* Join the `trades` and `users` tables using `user_id`.
* Filter only completed trades.
* Group the results by city.
* Count the number of completed orders in each city.
* Sort by the order count in descending order.
* Return only the top three cities.

---

## SQL Solution

```sql
SELECT
    u.city,
    COUNT(t.order_id) AS total_orders
FROM trades t
LEFT JOIN users u
    ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC
LIMIT 3;
```

---

# Query Explanation

## Step 1: Join the Tables

```sql
FROM trades t
LEFT JOIN users u
    ON t.user_id = u.user_id
```

The `trades` table contains trade information, while the `users` table contains user details such as their city.

The join matches each trade with the city of the user who placed it.

Example:

| order_id | user_id | city     |
| -------- | ------- | -------- |
| 101      | 1       | New York |
| 102      | 2       | Chicago  |
| 103      | 1       | New York |

---

## Step 2: Filter Completed Trades

```sql
WHERE t.status = 'Completed'
```

Only completed trade orders are considered.

Example:

| order_id | status    | Included |
| -------- | --------- | :------: |
| 101      | Completed |     ✅    |
| 102      | Cancelled |     ❌    |
| 103      | Completed |     ✅    |

---

## Step 3: Group by City

```sql
GROUP BY u.city
```

Rows belonging to the same city are grouped together.

Example:

Before grouping:

| city     |
| -------- |
| New York |
| New York |
| Chicago  |
| Boston   |

After grouping:

* New York
* Chicago
* Boston

---

## Step 4: Count Orders

```sql
COUNT(t.order_id)
```

Counts the number of completed trade orders for each city.

Example:

| city     | Orders |
| -------- | -----: |
| New York |     12 |
| Chicago  |      8 |
| Boston   |      5 |

---

## Step 5: Sort the Results

```sql
ORDER BY total_orders DESC
```

Orders the cities from the highest number of completed trades to the lowest.

---

## Step 6: Return Top Three Cities

```sql
LIMIT 3;
```

Returns only the first three rows after sorting.

---

## Alternative Approaches

### 1. Using `LIMIT` *(Recommended for PostgreSQL)*

```sql
SELECT
    u.city,
    COUNT(t.order_id) AS total_orders
FROM trades t
LEFT JOIN users u
    ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC
LIMIT 3;
```

**Database Support:** PostgreSQL, MySQL, SQLite

**Why use it?**

* Simple and concise.
* Most common interview solution.
* Efficient for Top-N queries.

---

### 2. Using `FETCH FIRST`

```sql
SELECT
    u.city,
    COUNT(t.order_id) AS total_orders
FROM trades t
LEFT JOIN users u
    ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC
FETCH FIRST 3 ROWS ONLY;
```

**Database Support:** PostgreSQL, Oracle, DB2

**Why use it?**

* ANSI SQL standard.
* More portable across SQL databases.

---

### 3. Using `TOP`

```sql
SELECT TOP 3
    u.city,
    COUNT(t.order_id) AS total_orders
FROM trades t
LEFT JOIN users u
    ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC;
```

**Database Support:** SQL Server

**Why use it?**

* Standard approach in Microsoft SQL Server.

---

## Understanding `LEFT JOIN`

A `LEFT JOIN` returns:

* All rows from the left table.
* Matching rows from the right table.
* `NULL` values when no match exists.

Syntax:

```sql
SELECT ...
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

Example:

**Trades**

| user_id |
| ------- |
| 1       |
| 2       |
| 3       |

**Users**

| user_id | city     |
| ------- | -------- |
| 1       | New York |
| 2       | Chicago  |

Result:

| user_id | city     |
| ------- | -------- |
| 1       | New York |
| 2       | Chicago  |
| 3       | NULL     |

> **Note:** Since every trade is expected to belong to a valid user, an `INNER JOIN` would also produce the same result for this problem.

---

## Understanding `COUNT()`

`COUNT()` returns the number of non-NULL values.

Syntax:

```sql
COUNT(column_name)
```

Example:

| order_id |
| -------- |
| 101      |
| 102      |
| 103      |

Result:

```text
COUNT(order_id) = 3
```

---

## Understanding `GROUP BY`

`GROUP BY` combines rows that share the same value.

```sql
GROUP BY city
```

Example:

| city     |
| -------- |
| New York |
| New York |
| Chicago  |

Produces two groups:

* New York
* Chicago

Aggregate functions are then calculated for each group.

---

## Understanding Top-N Queries

A **Top-N Query** returns only the highest or lowest `N` records after sorting.

Common syntax:

| Database           | Syntax                    |
| ------------------ | ------------------------- |
| PostgreSQL / MySQL | `LIMIT n`                 |
| SQL Server         | `TOP n`                   |
| Oracle / DB2       | `FETCH FIRST n ROWS ONLY` |

---

## Concepts

* LEFT JOIN
* Aggregate Functions (`COUNT`)
* `GROUP BY`
* `WHERE`
* `ORDER BY`
* `LIMIT`
* Top-N Queries

---

## Key Takeaways

* `LEFT JOIN` combines related data from multiple tables based on a matching key.
* `WHERE` filters rows before grouping and aggregation.
* `GROUP BY` creates one group for each city.
* `COUNT()` calculates the total number of completed trades in each group.
* `ORDER BY DESC` ranks cities from highest to lowest order count.
* `LIMIT` retrieves only the top **N** results after sorting.
* Top-N queries are a common SQL interview pattern and appear frequently in analytics and reporting scenarios.
