# Cards Issued Difference

**Platform:** DataLemur  
**Company:** JPMorgan Chase  
**Difficulty:** Easy  

---

## Problem

JPMorgan Chase wants to analyze the monthly issuance of its credit cards.

For each credit card:

- Find the **highest** number of cards issued in any month.
- Find the **lowest** number of cards issued in any month.
- Calculate the difference between them.
- Display the results in descending order of the difference.

Return:

- `card_name`
- `difference`

---

## Approach

- Group all records by `card_name`.
- Find the maximum number of cards issued using `MAX()`.
- Find the minimum number of cards issued using `MIN()`.
- Calculate the difference between the maximum and minimum values.
- Sort the result by the difference in descending order.

---

## SQL Solution

```sql
SELECT
    card_name,
    MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC;
```

---

# Query Explanation

## Step 1: Group Records by Card Name

```sql
GROUP BY card_name
```

### Why?

Each credit card has issuance records for multiple months.

Grouping by `card_name` allows SQL to calculate the highest and lowest issued amounts **independently for each card**.

### Example

Before grouping:

| card_name | issued_amount |
|-----------|--------------:|
|Gold|1000|
|Gold|1500|
|Silver|800|
|Silver|1200|

After grouping:

- Gold
- Silver

Each group is processed separately.

---

## Step 2: Find the Highest Monthly Issuance

```sql
MAX(issued_amount)
```

### Why?

`MAX()` returns the largest number of cards issued for each credit card.

This represents the month with the highest issuance.

### Example

| issued_amount |
|--------------:|
|1000|
|1500|
|1300|

Result:

```
MAX = 1500
```

---

## Step 3: Find the Lowest Monthly Issuance

```sql
MIN(issued_amount)
```

### Why?

`MIN()` returns the smallest number of cards issued for each credit card.

This represents the month with the lowest issuance.

### Example

| issued_amount |
|--------------:|
|1000|
|1500|
|1300|

Result:

```
MIN = 1000
```

---

## Step 4: Calculate the Difference

```sql
MAX(issued_amount) - MIN(issued_amount)
```

### Why?

The problem asks for the difference between the highest and lowest monthly issuance.

Subtracting the minimum from the maximum gives the issuance disparity for each card.

### Example

Highest issued:

```
1500
```

Lowest issued:

```
1000
```

Difference:

```
1500 - 1000 = 500
```

---

## Step 5: Sort the Results

```sql
ORDER BY difference DESC
```

### Why?

The problem requires the cards to be arranged based on the **largest disparity**.

Sorting in descending order places the cards with the greatest issuance variation at the top.

### Example

Before sorting:

| card_name | difference |
|-----------|-----------:|
|Silver|300|
|Gold|500|
|Platinum|200|

After sorting:

| card_name | difference |
|-----------|-----------:|
|Gold|500|
|Silver|300|
|Platinum|200|

---

## Alternative Approaches

### 1. Using `MAX()` and `MIN()` (Recommended)

```sql
SELECT
    card_name,
    MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC;
```

**Database Support:** All major SQL databases

**Why use it?**

- Simple and efficient.
- Requires only one aggregation.
- Most common interview solution.

---

### 2. Using a Common Table Expression (CTE)

```sql
WITH card_stats AS (
    SELECT
        card_name,
        MAX(issued_amount) AS max_issued,
        MIN(issued_amount) AS min_issued
    FROM monthly_cards_issued
    GROUP BY card_name
)

SELECT
    card_name,
    max_issued - min_issued AS difference
FROM card_stats
ORDER BY difference DESC;
```

**Database Support:** All major SQL databases

**Why use it?**

- Separates the aggregation from the calculation.
- Easier to extend if additional statistics are required.

---

## Understanding `MAX()`

`MAX()` returns the largest value in a group.

Example:

| issued_amount |
|--------------:|
|900|
|1500|
|1200|

Result:

```
MAX = 1500
```

---

## Understanding `MIN()`

`MIN()` returns the smallest value in a group.

Example:

| issued_amount |
|--------------:|
|900|
|1500|
|1200|

Result:

```
MIN = 900
```

---

## Understanding Aggregate Functions

Aggregate functions perform calculations on a group of rows and return a single value.

Examples include:

- `MAX()`
- `MIN()`
- `SUM()`
- `AVG()`
- `COUNT()`

In this problem:

```sql
MAX(issued_amount)
```

finds the highest issuance, while

```sql
MIN(issued_amount)
```

finds the lowest issuance.

---

## Understanding `GROUP BY`

`GROUP BY` groups rows having the same value.

Example:

Before grouping:

| card_name |
|-----------|
|Gold|
|Gold|
|Silver|

After grouping:

- Gold
- Silver

The aggregate functions (`MAX()` and `MIN()`) are then applied separately to each group.

---

## Concepts

- Aggregate Functions
- `MAX()`
- `MIN()`
- Arithmetic Operations
- `GROUP BY`
- `ORDER BY`

---

## Key Takeaways

- `MAX()` returns the largest value within each group.
- `MIN()` returns the smallest value within each group.
- Arithmetic expressions can directly combine aggregate functions.
- `GROUP BY` ensures calculations are performed independently for each card.
- `ORDER BY DESC` sorts the results from the largest difference to the smallest.
```