# Average Review Ratings

**Platform:** DataLemur 
**Company:** Amazon 
**Difficulty:** Easy  

---

## Problem

Calculate the **average star rating** for each product, grouped by the month in which the review was submitted.

The output should include:

* Month (numeric value)
* Product ID
* Average rating rounded to **2 decimal places**

Sort the results by **month** and then by **product ID**.

---

## Approach

* Extract the month from each review's submission date.
* Group reviews by **month** and **product ID**.
* Calculate the average rating for each group.
* Round the average to two decimal places.
* Sort the final output by month and product ID.

---

## SQL Solution

```sql
SELECT
    EXTRACT(MONTH FROM submit_date) AS mth,
    product_id,
    ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY
    EXTRACT(MONTH FROM submit_date),
    product_id
ORDER BY
    mth,
    product_id;
```

---

# Query Explanation

## Step 1: Extract the Month

```sql
EXTRACT(MONTH FROM submit_date)
```

The `EXTRACT()` function retrieves a specific part of a date or timestamp.

Example:

| submit_date | Month |
| ----------- | ----: |
| 2022-01-15  |     1 |
| 2022-03-20  |     3 |
| 2022-12-05  |    12 |

This extracted month is displayed as `mth`.

---

## Step 2: Group Reviews

```sql
GROUP BY
    EXTRACT(MONTH FROM submit_date),
    product_id
```

Rows are grouped by the combination of:

* Review Month
* Product ID

Example:

| Month | Product | Stars |
| ----: | ------- | ----: |
|     1 | 101     |     5 |
|     1 | 101     |     4 |
|     1 | 102     |     3 |
|     2 | 101     |     5 |

Groups become:

Group 1

| Month | Product |
| ----: | ------- |
|     1 | 101     |

Group 2

| Month | Product |
| ----: | ------- |
|     1 | 102     |

Group 3

| Month | Product |
| ----: | ------- |
|     2 | 101     |

Each group will have its own average rating.

---

## Step 3: Calculate the Average Rating

```sql
AVG(stars)
```

`AVG()` calculates the arithmetic mean.

Example:

| Stars |
| ----: |
|     5 |
|     4 |
|     3 |

Average

```text
(5 + 4 + 3) / 3 = 4
```

---

## Step 4: Round the Result

```sql
ROUND(AVG(stars), 2)
```

Rounds the average to **2 decimal places**.

Example:

|  Average | Rounded |
| -------: | ------: |
| 4.666666 |    4.67 |
|    3.125 |    3.13 |
|      4.0 |    4.00 |

---

## Step 5: Sort the Output

```sql
ORDER BY
    mth,
    product_id;
```

The result is first sorted by month and then by product ID.

Example:

| Month | Product |
| ----: | ------: |
|     1 |     101 |
|     1 |     102 |
|     2 |     101 |
|     2 |     205 |

---

## Alternative Approaches

### 1. Using `EXTRACT()` *(Recommended)*

```sql
SELECT
    EXTRACT(MONTH FROM submit_date) AS mth,
    product_id,
    ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY
    EXTRACT(MONTH FROM submit_date),
    product_id
ORDER BY
    mth,
    product_id;
```

**Database Support:** PostgreSQL, Oracle

**Why use it?**

* ANSI SQL compliant.
* Easy to understand.
* Commonly used in PostgreSQL.

---

### 2. Using `DATE_PART()`

```sql
SELECT
    DATE_PART('month', submit_date) AS mth,
    product_id,
    ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY
    DATE_PART('month', submit_date),
    product_id
ORDER BY
    mth,
    product_id;
```

**Database Support:** PostgreSQL

**Why use it?**

* PostgreSQL-specific alternative to `EXTRACT()`.
* Produces the same result.

---

### 3. Using `MONTH()`

```sql
SELECT
    MONTH(submit_date) AS mth,
    product_id,
    ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY
    MONTH(submit_date),
    product_id
ORDER BY
    mth,
    product_id;
```

**Database Support:** MySQL, SQL Server

**Why use it?**

* Very concise.
* Preferred in MySQL and SQL Server.

---

## Understanding `EXTRACT()`

The `EXTRACT()` function retrieves a specific part of a date or timestamp.

Syntax:

```sql
EXTRACT(field FROM source)
```

Examples:

```sql
EXTRACT(YEAR FROM submit_date)
```

Returns:

```text
2022
```

```sql
EXTRACT(MONTH FROM submit_date)
```

Returns:

```text
1
```

```sql
EXTRACT(DAY FROM submit_date)
```

Returns:

```text
15
```

Common fields:

| Field  | Description         |
| ------ | ------------------- |
| YEAR   | Extracts the year   |
| MONTH  | Extracts the month  |
| DAY    | Extracts the day    |
| HOUR   | Extracts the hour   |
| MINUTE | Extracts the minute |
| SECOND | Extracts the second |

---

## Understanding `AVG()`

`AVG()` calculates the arithmetic mean of numeric values.

Syntax:

```sql
AVG(column_name)
```

Example:

| Rating |
| -----: |
|      5 |
|      4 |
|      3 |

Result:

```text
AVG = 4.00
```

`AVG()` ignores `NULL` values automatically.

---

## Understanding `ROUND()`

`ROUND()` rounds a numeric value to a specified number of decimal places.

Syntax:

```sql
ROUND(number, decimal_places)
```

Examples:

|   Value | Rounded |
| ------: | ------: |
|  4.6666 |    4.67 |
| 3.14159 |    3.14 |
|   2.999 |    3.00 |

---

## Understanding Multiple `GROUP BY` Columns

```sql
GROUP BY
    EXTRACT(MONTH FROM submit_date),
    product_id
```

Rows are grouped based on the combination of both columns.

Example:

| Month | Product | Stars |
| ----: | ------- | ----: |
|     1 | 101     |     5 |
|     1 | 101     |     4 |
|     1 | 102     |     3 |

Creates two groups:

* Month 1 + Product 101
* Month 1 + Product 102

Each group receives its own average rating.

---

## Concepts

* Aggregate Functions (`AVG`)
* `ROUND()`
* `EXTRACT()`
* Date Functions
* `GROUP BY`
* Multiple Column Grouping
* `ORDER BY`

---

## Key Takeaways

* `EXTRACT()` retrieves specific components such as year, month, or day from a date or timestamp.
* `AVG()` computes the arithmetic mean while automatically ignoring `NULL` values.
* `ROUND()` formats numeric values to the required precision.
* Grouping by multiple columns creates unique groups based on the combination of those columns.
* Aggregate functions such as `AVG()` are calculated after `GROUP BY`.
* Sorting the results using `ORDER BY` ensures a consistent and readable output.
* Functions like `EXTRACT()`, `DATE_PART()`, and `MONTH()` serve similar purposes but differ depending on the SQL database being used.
