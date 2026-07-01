# Histogram of Tweets

**Platform:** DataLemur  
**Company:** Twitter  
**Difficulty:** Easy  

---

## Problem

Generate a histogram showing the number of tweets posted per user in **2022**.

The output should contain:

* `tweet_bucket` – Number of tweets posted by a user.
* `users_num` – Number of users who fall into that bucket.

---

## Approach

This problem requires **two levels of aggregation**:

1. Count the number of tweets posted by each user in 2022.
2. Group users based on their tweet count.
3. Count how many users belong to each tweet bucket.

---

## SQL Solution

```sql
SELECT
    tweet_count_per_user AS tweet_bucket,
    COUNT(user_id) AS users_num
FROM (
    SELECT
        user_id,
        COUNT(tweet_id) AS tweet_count_per_user
    FROM tweets
    WHERE tweet_date >= '2022-01-01'
      AND tweet_date < '2023-01-01'
    GROUP BY user_id
) AS total_tweets
GROUP BY tweet_count_per_user;
```

---

## Query Explanation

### Step 1: Filter Tweets from 2022

```sql
WHERE tweet_date >= '2022-01-01'
  AND tweet_date < '2023-01-01'
```

Only tweets posted during **2022** are included.

This approach is preferred because:

* Works with both `DATE` and `TIMESTAMP` columns.
* Allows the database to use indexes efficiently.
* Avoids applying functions to every row.

---

### Step 2: Count Tweets per User

```sql
SELECT
    user_id,
    COUNT(tweet_id) AS tweet_count_per_user
FROM tweets
GROUP BY user_id;
```

The inner query groups all tweets by `user_id` and calculates how many tweets each user posted.

Example:

| user_id | tweet_count_per_user |
| ------- | -------------------: |
| 101     |                    3 |
| 102     |                    1 |
| 103     |                    2 |
| 104     |                    1 |

---

### Step 3: Create the Histogram

```sql
SELECT
    tweet_count_per_user,
    COUNT(user_id)
FROM (...)
GROUP BY tweet_count_per_user;
```

The outer query groups users by their tweet count.

Example:

| user_id | tweet_count_per_user |
| ------- | -------------------: |
| 101     |                    3 |
| 102     |                    1 |
| 103     |                    2 |
| 104     |                    1 |
| 105     |                    3 |

Result:

| tweet_bucket | users_num |
| ------------ | --------: |
| 1            |         2 |
| 2            |         1 |
| 3            |         2 |

---

## Alternative Approaches

### 1. Using a Common Table Expression (CTE)

```sql
WITH tweet_counts AS (
    SELECT
        user_id,
        COUNT(tweet_id) AS tweet_count_per_user
    FROM tweets
    WHERE tweet_date >= '2022-01-01'
      AND tweet_date < '2023-01-01'
    GROUP BY user_id
)

SELECT
    tweet_count_per_user AS tweet_bucket,
    COUNT(*) AS users_num
FROM tweet_counts
GROUP BY tweet_count_per_user;
```

**Why use it?**

* Improves readability.
* Separates the query into logical steps.
* Preferred for complex analytical queries.

---

### 2. Using a Derived Table *(Current Solution)*

```sql
SELECT
    tweet_count_per_user AS tweet_bucket,
    COUNT(user_id) AS users_num
FROM (
    SELECT
        user_id,
        COUNT(tweet_id) AS tweet_count_per_user
    FROM tweets
    WHERE tweet_date >= '2022-01-01'
      AND tweet_date < '2023-01-01'
    GROUP BY user_id
) AS total_tweets
GROUP BY tweet_count_per_user;
```

**Why use it?**

* Standard SQL syntax.
* Supported by all major SQL databases.
* Commonly used in SQL interviews.

---

### 3. Using `BETWEEN`

```sql
WHERE tweet_date::DATE BETWEEN '2022-01-01' AND '2022-12-31'
```

**Why use it?**

* Easy to read.
* Includes both start and end dates.
* Suitable when working with `DATE` values.

> **Note:** If `tweet_date` is a `TIMESTAMP`, casting it to `DATE` removes the time component. However, applying a function to the column may prevent efficient index usage. For better performance, prefer date range filtering.

---

## Understanding the Subquery

The query uses a **Derived Table**, which is a type of **Subquery**.

```sql
FROM (
    SELECT
        user_id,
        COUNT(tweet_id) AS tweet_count_per_user
    FROM tweets
    WHERE tweet_date >= '2022-01-01'
      AND tweet_date < '2023-01-01'
    GROUP BY user_id
) AS total_tweets
```

A **Subquery** is a query nested inside another SQL query. In this problem:

* The **inner query** calculates the total number of tweets posted by each user.
* The **outer query** groups users by that tweet count to build the histogram.

Without the subquery, the second aggregation would not be possible because SQL cannot directly group by an aggregate value created in the same query.

## Types of Subqueries

| Type                           | Description                                                           |
| ------------------------------ | --------------------------------------------------------------------- |
| Scalar Subquery                | Returns a single value.                                               |
| Row Subquery                   | Returns one row.                                                      |
| Column Subquery                | Returns one column.                                                   |
| Table (Derived Table) Subquery | Returns multiple rows and columns and behaves like a temporary table. |
| Correlated Subquery            | References the outer query and executes once for each outer row.      |

This solution uses a **Table (Derived Table) Subquery**.

---

## Key Takeaways

* Some SQL problems require multiple levels of aggregation.
* Subqueries and CTEs help break complex problems into smaller, manageable steps.
* A **Derived Table** is a subquery used in the `FROM` clause and behaves like a temporary table.
* `GROUP BY` can be applied multiple times to summarize data at different levels.
* Filtering with a date range (`>=` and `<`) is generally preferred over `BETWEEN` for `TIMESTAMP` columns because it is more index-friendly and avoids issues with time values.

---
## Understanding Common Table Expressions (CTEs)

A **Common Table Expression (CTE)** is a temporary named result set created using the `WITH` clause. It exists only for the duration of the query and can be referenced like a regular table.

Unlike a permanent table, a CTE is **not stored in the database**. It is created when the query starts executing and is automatically discarded after the query finishes.

---

### Basic Syntax

```sql
WITH cte_name AS (
    SELECT ...
)
SELECT *
FROM cte_name;
```

* `WITH` begins the CTE definition.
* `cte_name` is the temporary table name.
* The query inside the parentheses generates the data stored in the CTE.
* The main query can reference the CTE just like a normal table.

---

## How a CTE Works

Consider the following query:

```sql
WITH tweet_counts AS (
    SELECT
        user_id,
        COUNT(tweet_id) AS tweet_count_per_user
    FROM tweets
    WHERE tweet_date >= '2022-01-01'
      AND tweet_date < '2023-01-01'
    GROUP BY user_id
)

SELECT
    tweet_count_per_user AS tweet_bucket,
    COUNT(*) AS users_num
FROM tweet_counts
GROUP BY tweet_count_per_user;
```

### Step 1: Create the CTE

The database first executes everything inside the `WITH` clause.

```sql
SELECT
    user_id,
    COUNT(tweet_id) AS tweet_count_per_user
FROM tweets
WHERE tweet_date >= '2022-01-01'
  AND tweet_date < '2023-01-01'
GROUP BY user_id;
```

Temporary Result (`tweet_counts`):

| user_id | tweet_count_per_user |
| ------- | -------------------: |
| 101     |                    3 |
| 102     |                    1 |
| 103     |                    2 |
| 104     |                    1 |

At this point, `tweet_counts` behaves like a temporary table.

---

### Step 2: Execute the Main Query

The main query now treats `tweet_counts` as its source table.

```sql
SELECT
    tweet_count_per_user,
    COUNT(*)
FROM tweet_counts
GROUP BY tweet_count_per_user;
```

Result:

| tweet_bucket | users_num |
| ------------ | --------: |
| 1            |         2 |
| 2            |         1 |
| 3            |         1 |

The histogram is now complete.

---

## Why Use a CTE?

CTEs make SQL easier to read by breaking a complex query into smaller logical steps.

Instead of writing one large nested query, each step can be named and understood independently.

Benefits include:

* Improves readability.
* Makes complex queries easier to debug.
* Allows the same intermediate result to be referenced multiple times.
* Helps organize analytical queries into logical stages.
* Supports recursive queries using `WITH RECURSIVE`.

---

## CTE vs Subquery

| Common Table Expression (CTE)                           | Subquery (Derived Table)                                  |
| ------------------------------------------------------- | --------------------------------------------------------- |
| Defined using the `WITH` clause.                        | Defined inside the `FROM` clause.                         |
| Can be referenced multiple times within the same query. | Usually referenced only once.                             |
| Easier to read for large queries.                       | Better suited for short queries.                          |
| Supports recursive queries.                             | Does not support recursion.                               |
| Often preferred for analytical SQL.                     | Common in interview questions and simple transformations. |

Both approaches produce the same result in this problem.

---

## When Should You Use a CTE?

A CTE is a good choice when:

* Breaking a complex query into multiple logical steps.
* Reusing an intermediate result multiple times.
* Improving query readability.
* Building recursive queries.
* Performing multi-step data transformations.

For short one-time calculations, a derived table is often sufficient.

---

## Common Mistakes

### Assuming a CTE creates a permanent table.

A CTE only exists while the query is running.

---

### Thinking a CTE always improves performance.

A CTE primarily improves readability. Depending on the database optimizer, it may or may not affect execution performance.

---

### Forgetting that the CTE must appear before the main query.

The `WITH` clause is always written before the query that references it.

---

## Concepts

* Subqueries
* Derived Tables
* Common Table Expressions (CTEs)
* Multi-Level Aggregation
* Aggregate Functions (`COUNT`)
* `GROUP BY`
* Date Range Filtering
* `BETWEEN`

---

## Key Takeaways

* Some SQL problems require **multiple levels of aggregation**, where the result of one aggregation is used as the input for another.
* **CTEs** and **subqueries** help break complex queries into smaller, logical, and manageable steps.
* A **CTE** is a temporary named result set created using the `WITH` clause, while a **Derived Table** is a subquery defined in the `FROM` clause. Both behave like temporary tables during query execution.
* CTEs improve **readability**, **maintainability**, and can be referenced multiple times within the same query.
* `GROUP BY` can be applied multiple times to summarize data at different levels, making it useful for analytical problems such as histograms and multi-stage aggregations.
* For filtering `TIMESTAMP` columns, using a **date range** (`>=` and `<`) is generally preferred over `BETWEEN` because it is more index-friendly and correctly handles time values.
* Choose the approach—CTE or derived table—that makes your query easier to understand while considering database compatibility and performance.
