# Data Science Skills

**Platform:** DataLemur 
**Company:** LinkedIn 
**Difficulty:** Easy 

---

## Problem

Find the candidates who possess **all three required skills** for a Data Science role:

* Python
* Tableau
* PostgreSQL

Return the `candidate_id` of qualified candidates in ascending order.

---

## Approach

* Filter the rows to keep only the required skills.
* Group records by `candidate_id`.
* Count the number of required skills each candidate possesses.
* Return only candidates with all three required skills.

---

## SQL Solution

```sql
SELECT
    candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
ORDER BY candidate_id;
```

---

# Query Explanation

## Step 1: Filter Required Skills

```sql
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
```

The `IN` operator filters the table to include only the required skills.

Example:

| candidate_id | skill      |
| ------------ | ---------- |
| 101          | Python     |
| 101          | Tableau    |
| 101          | PostgreSQL |
| 101          | Excel      |

After filtering:

| candidate_id | skill      |
| ------------ | ---------- |
| 101          | Python     |
| 101          | Tableau    |
| 101          | PostgreSQL |

The `Excel` row is ignored because it is not required.

---

## Step 2: Group by Candidate

```sql
GROUP BY candidate_id
```

All skills belonging to the same candidate are grouped together.

Example:

Candidate **101**

| skill      |
| ---------- |
| Python     |
| Tableau    |
| PostgreSQL |

Candidate **102**

| skill   |
| ------- |
| Python  |
| Tableau |

Each candidate now becomes one group.

---

## Step 3: Count the Skills

```sql
COUNT(skill)
```

Counts the number of required skills each candidate has after filtering.

Example:

| candidate_id | Required Skills             | Count |
| ------------ | --------------------------- | ----: |
| 101          | Python, Tableau, PostgreSQL |     3 |
| 102          | Python, Tableau             |     2 |
| 103          | Python                      |     1 |

---

## Step 4: Keep Candidates with All Skills

```sql
HAVING COUNT(skill) = 3
```

`HAVING` filters the grouped results.

Only candidates with **all three required skills** are returned.

Result:

| candidate_id |
| ------------ |
| 101          |
| 205          |

---

## Step 5: Sort the Result

```sql
ORDER BY candidate_id;
```

Returns the qualified candidates in ascending order.

---

## Alternative Approaches

### 1. Using `GROUP BY` + `HAVING`  *(Recommended)*

```sql
SELECT
    candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
ORDER BY candidate_id;
```

**Database Support:** All major SQL databases

**Why use it?**

* Simple and efficient.
* Most common interview solution.
* Easy to understand.
* Works across all major SQL databases.

---

### 2. Using Conditional Aggregation

```sql
SELECT
    candidate_id
FROM candidates
GROUP BY candidate_id
HAVING
    SUM(CASE WHEN skill = 'Python' THEN 1 ELSE 0 END) > 0
AND SUM(CASE WHEN skill = 'Tableau' THEN 1 ELSE 0 END) > 0
AND SUM(CASE WHEN skill = 'PostgreSQL' THEN 1 ELSE 0 END) > 0
ORDER BY candidate_id;
```

**Database Support:** All major SQL databases

**Why use it?**

* Explicitly checks for each required skill.
* Useful when different conditions need to be validated independently.
* Easily extended for more complex business rules.

---

### 3. Using `COUNT(DISTINCT)`

```sql
SELECT
    candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3
ORDER BY candidate_id;
```

**Database Support:** All major SQL databases

**Why use it?**

* Prevents duplicate skills from affecting the result.
* More robust if duplicate rows exist.
* Commonly used in production databases.

> **Note:** This problem states that there are no duplicate records, so `COUNT(skill)` and `COUNT(DISTINCT skill)` produce the same result.

---

## Understanding the `IN` Operator

The `IN` operator checks whether a value matches any value in a list.

Syntax:

```sql
WHERE column_name IN (value1, value2, value3)
```

Example:

```sql
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
```

Equivalent to:

```sql
WHERE skill = 'Python'
   OR skill = 'Tableau'
   OR skill = 'PostgreSQL'
```

Using `IN` makes the query shorter, cleaner, and easier to maintain.

---

## Understanding `HAVING`

`HAVING` filters groups after aggregation.

Unlike `WHERE`, it can use aggregate functions such as:

* `COUNT()`
* `SUM()`
* `AVG()`
* `MAX()`
* `MIN()`

Execution order:

1. `FROM`
2. `WHERE`
3. `GROUP BY`
4. `HAVING`
5. `SELECT`
6. `ORDER BY`

Since `COUNT(skill)` is calculated after grouping, it must be used inside `HAVING`, not `WHERE`.

---

## Concepts

* Filtering (`WHERE`)
* `IN`
* `GROUP BY`
* `HAVING`
* Aggregate Functions (`COUNT`)
* Conditional Aggregation
* `COUNT(DISTINCT)`
* Sorting (`ORDER BY`)

---

## Key Takeaways

* Use the `IN` operator to filter multiple values in a concise and readable way.
* `GROUP BY` groups rows belonging to the same candidate before aggregation.
* `HAVING` filters grouped results based on aggregate functions such as `COUNT()`.
* `COUNT(DISTINCT)` is safer than `COUNT()` when duplicate records may exist.
* Conditional aggregation using `SUM(CASE WHEN...)` is useful when checking multiple independent conditions.
* Understanding the SQL execution order helps explain why aggregate functions belong in the `HAVING` clause rather than `WHERE`.
