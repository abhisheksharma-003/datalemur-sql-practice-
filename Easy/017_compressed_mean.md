# Compressed Mean

**Platform:** DataLemur  
**Company:** Alibaba  
**Difficulty:** Easy  

---

## Problem

Alibaba stores order data in a compressed format.

Instead of storing every individual order, the table contains:

- `item_count` – the number of items in an order.
- `order_occurrences` – how many orders contain that number of items.

Calculate the **mean (average) number of items per order**, rounded to **1 decimal place**.

Return:

- `mean`

---

## Approach

- Multiply each `item_count` by its `order_occurrences` to calculate the total number of items.
- Sum all the items across every group.
- Sum all order occurrences to get the total number of orders.
- Divide the total number of items by the total number of orders.
- Round the result to one decimal place.

---

## SQL Solution

```sql
SELECT
    ROUND(
        SUM(item_count::DECIMAL * order_occurrences)
        / SUM(order_occurrences),
        1
    ) AS mean
FROM items_per_order;
```

---

# Query Explanation

## Step 1: Calculate the Total Number of Items

```sql
SUM(item_count::DECIMAL * order_occurrences)
```

### Why?

The table stores data in a compressed format.

Instead of listing every order individually, one row represents many identical orders.

To find the total number of items sold, multiply:

```
Items per Order × Number of Orders
```

### Example

| item_count | order_occurrences |
|-----------:|------------------:|
|1|500|
|2|1000|
|3|800|

Calculation:

```
1 × 500 = 500

2 × 1000 = 2000

3 × 800 = 2400

Total Items = 4900
```

---

## Step 2: Calculate the Total Number of Orders

```sql
SUM(order_occurrences)
```

### Why?

The denominator of the mean formula is the **total number of orders**.

Since each row represents multiple orders, we sum all occurrences.

### Example

| order_occurrences |
|------------------:|
|500|
|1000|
|800|

Calculation:

```
500 + 1000 + 800

= 2300 orders
```

---

## Step 3: Calculate the Mean

```sql
SUM(item_count * order_occurrences)
/
SUM(order_occurrences)
```

### Why?

The average is calculated using the standard formula:

```
Mean =
Total Items
────────────
Total Orders
```

Since the data is compressed, we use a **weighted average** instead of a simple average.

### Example

```
Total Items = 4900

Total Orders = 2300

Mean = 4900 / 2300

= 2.13
```

---

## Step 4: Convert to DECIMAL

```sql
item_count::DECIMAL
```

### Why?

Both `item_count` and `order_occurrences` are integers.

Without converting one operand to a decimal type, many SQL databases perform **integer division**, which removes the decimal part.

Casting ensures floating-point division and produces an accurate average.

### Example

Without casting:

```sql
5 / 2
```

Result:

```
2
```

With casting:

```sql
5::DECIMAL / 2
```

Result:

```
2.5
```

---

## Step 5: Round the Result

```sql
ROUND(expression, 1)
```

### Why?

The problem asks for the answer rounded to **one decimal place**.

### Example

Before rounding:

```
2.13478
```

After rounding:

```
2.1
```

---

## Alternative Approaches

### 1. Using Weighted Average (Recommended)

```sql
SELECT
    ROUND(
        SUM(item_count::DECIMAL * order_occurrences)
        / SUM(order_occurrences),
        1
    ) AS mean
FROM items_per_order;
```

**Database Support:** PostgreSQL

**Why use it?**

- Short and efficient.
- Directly computes the weighted mean.
- Most common interview solution.

---

### 2. Using `CAST()`

```sql
SELECT
    ROUND(
        SUM(CAST(item_count AS DECIMAL) * order_occurrences)
        / SUM(order_occurrences),
        1
    ) AS mean
FROM items_per_order;
```

**Database Support:** All major SQL databases

**Why use it?**

- More portable than PostgreSQL's `::DECIMAL` syntax.
- Preferred in SQL Server, MySQL, and Oracle.

---

## Understanding Weighted Average

A weighted average assigns different importance (weights) to values.

Formula:

```
Weighted Average =
Σ(Value × Weight)
──────────────────
Σ(Weight)
```

In this problem:

- Value = `item_count`
- Weight = `order_occurrences`

---

## Understanding `SUM()`

`SUM()` calculates the total of numeric values.

Example:

| Value |
|------:|
|500|
|2000|
|2400|

Result:

```
SUM = 4900
```

---

## Understanding Type Casting

Type casting converts one data type into another.

Example:

```sql
item_count::DECIMAL
```

or

```sql
CAST(item_count AS DECIMAL)
```

Casting prevents integer division and preserves decimal precision.

---

## Understanding `ROUND()`

`ROUND(number, decimal_places)` rounds a numeric value to the specified number of decimal places.

Example:

```sql
ROUND(2.137, 1)
```

Result:

```
2.1
```

---

## Concepts

- Aggregate Functions (`SUM`)
- Weighted Average
- Type Casting
- Integer vs Decimal Division
- Arithmetic Expressions
- `ROUND()`

---

## Key Takeaways

- Compressed data often requires a **weighted average** rather than a simple average.
- Multiply each value by its frequency before summing.
- Divide the total weighted value by the total frequency.
- Cast to a decimal type to avoid integer division.
- Use `ROUND()` to match the required output format.
```