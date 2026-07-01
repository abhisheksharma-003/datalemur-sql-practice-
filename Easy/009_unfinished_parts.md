# Unfinished Parts

**Platform:** DataLemur 
**Company:** Tesla 
**Difficulty:** Easy 

---

## Problem

Retrieve all parts that have **started the assembly process but have not yet been completed**.

Return:

* `part`
* `assembly_step`

An unfinished part is identified by a `NULL` value in the `finish_date` column.

---

## Approach

* Retrieve the required columns.
* Filter rows where `finish_date` is `NULL`.
* Return only unfinished parts.

---

## SQL Solution

```sql
SELECT
    part,
    assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;
```

---

# Query Explanation

## Step 1: Select the Required Columns

```sql
SELECT
    part,
    assembly_step
```

Only the required columns are selected.

Example:

| part   | assembly_step |
| ------ | ------------- |
| Bumper | Painting      |
| Door   | Assembly      |

Selecting only the necessary columns improves readability and avoids returning unnecessary data.

---

## Step 2: Read from the Table

```sql
FROM parts_assembly
```

The query retrieves data from the `parts_assembly` table.

Example:

| part   | assembly_step | finish_date |
| ------ | ------------- | ----------- |
| Door   | Assembly      | 2022-08-10  |
| Bumper | Painting      | NULL        |
| Mirror | Inspection    | NULL        |

---

## Step 3: Filter Unfinished Parts

```sql
WHERE finish_date IS NULL
```

A `NULL` value indicates that the assembly process has not yet been completed.

Example:

Before filtering:

| part   | finish_date |
| ------ | ----------- |
| Door   | 2022-08-10  |
| Bumper | NULL        |
| Mirror | NULL        |

After filtering:

| part   | finish_date |
| ------ | ----------- |
| Bumper | NULL        |
| Mirror | NULL        |

Only unfinished parts remain.

---

## Alternative Approaches

### 1. Using `IS NULL`  *(Recommended)*

```sql
SELECT
    part,
    assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;
```

**Database Support:** All major SQL databases

**Why use it?**

* Standard SQL syntax.
* Most efficient and readable approach.
* Supported by every relational database.

---

### 2. Using `COALESCE()`

```sql
SELECT
    part,
    assembly_step
FROM parts_assembly
WHERE COALESCE(finish_date, '9999-12-31') = '9999-12-31';
```

**Database Support:** PostgreSQL, MySQL, SQL Server, Oracle

**Why use it?**

* Replaces `NULL` with a default value before comparison.
* Useful when performing calculations involving `NULL` values.

> **Note:** Although this works, using `IS NULL` is simpler, clearer, and generally preferred.

---

## Understanding `NULL`

`NULL` represents **missing, unknown, or unavailable data**.

It is **not**:

* Zero (`0`)
* An empty string (`''`)
* False (`FALSE`)

Example:

| part   | finish_date |
| ------ | ----------- |
| Door   | 2022-08-10  |
| Bumper | NULL        |

Here, `NULL` means the finish date is **unknown or not yet assigned**.

---

## Why Can't We Use `= NULL`?

This is one of the most common SQL mistakes.

❌ Incorrect

```sql
WHERE finish_date = NULL;
```

This returns **no rows**.

SQL treats `NULL` as an unknown value, so comparisons using `=` or `!=` do not work.

Instead, SQL provides special operators.

✅ Correct

```sql
WHERE finish_date IS NULL;
```

or

```sql
WHERE finish_date IS NOT NULL;
```

---

## Understanding `IS NULL`

The `IS NULL` operator checks whether a column contains a `NULL` value.

Syntax:

```sql
WHERE column_name IS NULL;
```

Example:

| finish_date | Matches? |
| ----------- | :------: |
| NULL        |     ✅    |
| 2022-08-10  |     ❌    |
| 2023-01-15  |     ❌    |

---

## Understanding `COALESCE()`

`COALESCE()` returns the **first non-NULL value** from a list of expressions.

Syntax:

```sql
COALESCE(value1, value2, value3, ...)
```

Example:

```sql
SELECT
    COALESCE(finish_date, CURRENT_DATE)
FROM parts_assembly;
```

If `finish_date` is `NULL`, today's date is returned instead.

This function is commonly used to replace missing values during calculations or reporting.

---

## Concepts

* Filtering (`WHERE`)
* `NULL`
* `IS NULL`
* `COALESCE()`
* Data Cleaning

---

## Key Takeaways

* `NULL` represents missing or unknown data and is different from `0` or an empty string.
* SQL uses `IS NULL` and `IS NOT NULL` to test for missing values; `= NULL` does not work.
* `IS NULL` is the standard and preferred way to filter rows with missing values.
* `COALESCE()` replaces `NULL` with the first available non-NULL value and is useful for reporting and calculations.
* Selecting only the required columns keeps queries efficient and improves readability.
