# Average Post Hiatus (Part 1)

**Platform:** DataLemur  
**Company:** Facebook  
**Difficulty:** Easy  

---

## Problem

For each user who made **at least two posts in 2021**, calculate the number of days between their **first** and **last** post during 2021.

---

## Approach

* Filter posts made in **2021**.
* Group posts by `user_id`.
* Find the earliest and latest post date for each user.
* Calculate the difference between the two dates.
* Return only users with more than one post.

---

## SQL Solution

```sql
SELECT
    user_id,
    MAX(post_date::DATE) - MIN(post_date::DATE) AS days_between
FROM posts
WHERE DATE_PART('year', post_date::DATE) = 2021
GROUP BY user_id
HAVING COUNT(post_id) > 1;
```

---

## Query Explanation

### 1. Filter Posts from 2021

```sql
WHERE DATE_PART('year', post_date::DATE) = 2021
```

Extracts the year from `post_date` and keeps only posts created in **2021**.

Example:

| post_date  | Included |
| ---------- | :------: |
| 2021-04-15 |     ✅    |
| 2022-01-08 |     ❌    |

---

### 2. Group by User

```sql
GROUP BY user_id
```

Groups all posts belonging to the same user so aggregate functions can be applied.

Example:

| user_id | post_date  |
| ------- | ---------- |
| 101     | 2021-01-10 |
| 101     | 2021-05-12 |
| 101     | 2021-12-20 |

becomes one group for **User 101**.

---

### 3. Find the First and Last Post

```sql
MAX(post_date::DATE)
```

Returns the latest post date.

```sql
MIN(post_date::DATE)
```

Returns the earliest post date.

Example:

| Function | Result     |
| -------- | ---------- |
| MIN()    | 2021-01-10 |
| MAX()    | 2021-12-20 |

---

### 4. Calculate the Hiatus

```sql
MAX(post_date::DATE) - MIN(post_date::DATE)
```

Subtracting two `DATE` values in PostgreSQL returns the number of days between them.

Example:

```text
2021-12-20
-
2021-01-10
-----------
344 days
```

The result is returned as `days_between`.

---

### 5. Keep Users with Multiple Posts

```sql
HAVING COUNT(post_id) > 1
```

Filters grouped results to include only users who posted more than once.

Example:

| user_id | Posts | Included |
| ------- | ----: | :------: |
| 101     |     3 |     ✅    |
| 102     |     1 |     ❌    |
| 103     |     2 |     ✅    |

---

## Alternative Approaches

### 1. Using `EXTRACT()`

```sql
WHERE EXTRACT(YEAR FROM post_date) = 2021
```

**Why use it?**

* ANSI SQL compliant.
* More portable across SQL databases.

---

### 2. Using `CAST()`

```sql
MAX(CAST(post_date AS DATE)) -
MIN(CAST(post_date AS DATE))
```

**Why use it?**

* Equivalent to PostgreSQL's `::DATE`.
* Easier to understand if you're unfamiliar with PostgreSQL shorthand.

---

### 3. Using Date Range Filtering *(Recommended)*

```sql
WHERE post_date >= '2021-01-01'
  AND post_date < '2022-01-01'
```

**Why use it?**

* Often performs better because it can take advantage of indexes on `post_date`.
* Avoids applying a function to every row.

---

## Concepts

* Aggregate Functions (`MIN`, `MAX`, `COUNT`)
* Date Arithmetic
* `GROUP BY`
* `HAVING`
* `DATE_PART()`
* `EXTRACT()`
* Type Casting (`::DATE`)
* Date Range Filtering

---

## Key Takeaways

* `MIN()` and `MAX()` are useful for finding the first and last occurrence within a group.
* PostgreSQL allows direct subtraction of two `DATE` values to calculate the number of days between them.
* `HAVING` filters grouped results, whereas `WHERE` filters individual rows before grouping.
* Filtering with a date range (`>= '2021-01-01' AND < '2022-01-01'`) is generally preferred over extracting the year because it is more index-friendly.
