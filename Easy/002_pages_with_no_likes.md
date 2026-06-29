# Page With No Likes

**Platform:** DataLemur
**Company:** Facebook
**Difficulty:** Easy

---

## Problem

Return the IDs of all Facebook pages that have **zero likes**.

The output should be sorted in **ascending order** by `page_id`.

---

## Approach

* Retrieve all pages from the `pages` table.
* Perform a `LEFT JOIN` with the `page_likes` table.
* Identify pages with no matching likes using `IS NULL`.
* Sort the results by `page_id`.

---

## SQL Solution

```sql
SELECT
    p.page_id
FROM pages AS p
LEFT JOIN page_likes AS l
    ON p.page_id = l.page_id
WHERE l.page_id IS NULL
ORDER BY p.page_id;
```

---

## Alternative Approaches

### 1. Using `LEFT JOIN` + `IS NULL` *(Recommended)*

```sql
SELECT
    p.page_id
FROM pages AS p
LEFT JOIN page_likes AS l
    ON p.page_id = l.page_id
WHERE l.page_id IS NULL
ORDER BY p.page_id;
```

**Why use it?**

* Most common anti-join pattern.
* Easy to understand.
* Works across almost all SQL databases.

---

### 2. Using `NOT EXISTS`

```sql
SELECT
    p.page_id
FROM pages AS p
WHERE NOT EXISTS (
    SELECT 1
    FROM page_likes AS l
    WHERE l.page_id = p.page_id
)
ORDER BY p.page_id;
```

**Why use it?**

* Clearly expresses the intent of finding rows with no matching records.
* Performs well in many database systems.

---

### 3. Using `EXCEPT` *(PostgreSQL / SQL Server)*

```sql
SELECT page_id
FROM pages

EXCEPT

SELECT page_id
FROM page_likes

ORDER BY page_id;
```

**Why use it?**
- Very concise and expressive.
- Ideal when you want the set difference between two query results.
- Supported in PostgreSQL and SQL Server.

> **Note:** `EXCEPT` is not available in MySQL. For cross-database compatibility, prefer `LEFT JOIN ... IS NULL` or `NOT EXISTS`.

---

### 4. Using `NOT IN`

```sql
SELECT
    page_id
FROM pages
WHERE page_id NOT IN (
    SELECT page_id
    FROM page_likes
)
ORDER BY page_id;
```

**Why use it?**

* Concise and easy to read.
* Suitable when the subquery is guaranteed not to return `NULL` values.

> **Note:** `NOT IN` can produce unexpected results if the subquery contains `NULL` values. In real-world scenarios, `LEFT JOIN ... IS NULL` or `NOT EXISTS` is generally preferred.

---

## Concepts

* LEFT JOIN
* Anti Join
* IS NULL
* NOT EXISTS
* EXCEPT
* NOT IN
* ORDER BY

