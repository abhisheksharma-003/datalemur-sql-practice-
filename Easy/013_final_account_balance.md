# Final Account Balance

**Platform:** DataLemur 
**Company:** PayPal 
**Difficulty:** Easy 

---

## Problem

Calculate the **final balance** for each account based on all recorded transactions.

Assumptions:

- A **Deposit** increases the account balance.
- A **Withdrawal** decreases the account balance.
- There are no missing transactions.

Return:

- `account_id`
- `balance_amount`

---

## Approach

- Group transactions by `account_id`.
- Use a `CASE WHEN` statement to identify the transaction type.
- Add the transaction amount for deposits.
- Subtract the transaction amount for withdrawals.
- Sum all transaction values to compute the final balance.

---

## SQL Solution

```sql
SELECT
    account_id,
    SUM(
        CASE
            WHEN transaction_type = 'Deposit'
            THEN amount
            ELSE -amount
        END
    ) AS balance_amount
FROM transactions
GROUP BY account_id;
```

---

# Query Explanation

## Step 1: Group Transactions by Account

```sql
GROUP BY account_id
```

Groups all transactions belonging to the same account.

Example:

| account_id | transaction_type | amount |
|-----------:|------------------|-------:|
|101|Deposit|500|
|101|Withdrawal|150|
|101|Deposit|200|

All three transactions belong to Account **101**.

---

## Step 2: Identify Deposits and Withdrawals

```sql
CASE
    WHEN transaction_type = 'Deposit'
    THEN amount
    ELSE -amount
END
```

The `CASE` expression converts every transaction into its effect on the account balance.

| Transaction | Returned Value |
|-------------|---------------:|
|Deposit 500|+500|
|Withdrawal 150|-150|
|Deposit 200|+200|

Notice that withdrawals become negative values.

---

## Step 3: Calculate the Final Balance

```sql
SUM(...)
```

Adds all positive and negative transaction values.

Example:

```
+500
-150
+200
------
550
```

Final balance = **550**

---

## Step 4: Return the Result

Output:

| account_id | balance_amount |
|-----------:|---------------:|
|101|550|
|102|780|

Each account appears exactly once.

---

## Alternative Approaches

### 1. Using `SUM(CASE WHEN ...)` *(Recommended)*

```sql
SELECT
    account_id,
    SUM(
        CASE
            WHEN transaction_type = 'Deposit'
            THEN amount
            ELSE -amount
        END
    ) AS balance_amount
FROM transactions
GROUP BY account_id;
```

**Database Support:** All major SQL databases

**Why use it?**

- Simple and efficient.
- Most common interview solution.
- Works in every major SQL database.

---

### 2. Using Separate Conditions

```sql
SELECT
    account_id,
    SUM(
        CASE
            WHEN transaction_type = 'Deposit'
            THEN amount
            ELSE 0
        END
    )
    -
    SUM(
        CASE
            WHEN transaction_type = 'Withdrawal'
            THEN amount
            ELSE 0
        END
    ) AS balance_amount
FROM transactions
GROUP BY account_id;
```

**Database Support:** All SQL databases

**Why use it?**

- Explicitly separates deposits and withdrawals.
- Useful when multiple transaction types exist.

---

### 3. Using `FILTER` *(PostgreSQL)*

```sql
SELECT
    account_id,
    SUM(amount) FILTER (
        WHERE transaction_type = 'Deposit'
    )
    -
    SUM(amount) FILTER (
        WHERE transaction_type = 'Withdrawal'
    ) AS balance_amount
FROM transactions
GROUP BY account_id;
```

**Database Support:** PostgreSQL

**Why use it?**

- Cleaner syntax.
- Easy to understand.
- Avoids lengthy `CASE WHEN` expressions.

---

## Understanding `CASE WHEN`

`CASE WHEN` allows SQL to perform conditional logic.

Syntax:

```sql
CASE
    WHEN condition THEN value
    ELSE value
END
```

Example:

```sql
CASE
    WHEN transaction_type = 'Deposit'
    THEN amount
    ELSE -amount
END
```

If the transaction is a deposit, the amount is added.

Otherwise, the amount is treated as a negative value.

---

## Understanding `SUM()`

`SUM()` calculates the total of numeric values.

Example:

| Value |
|------:|
|500|
|-150|
|200|

Result:

```
SUM = 550
```

---

## Understanding Conditional Aggregation

Conditional aggregation combines aggregate functions with conditional logic.

Example:

```sql
SUM(
    CASE
        WHEN transaction_type = 'Deposit'
        THEN amount
        ELSE -amount
    END
)
```

Instead of filtering rows first, each row contributes a different value depending on its condition.

---

## Understanding `GROUP BY`

`GROUP BY` creates one group for each account.

Example:

Before grouping:

| account_id |
|-----------:|
|101|
|101|
|102|

After grouping:

- Account 101
- Account 102

The aggregate function (`SUM`) is then applied to each group separately.

---

## Concepts

- Aggregate Functions (`SUM`)
- `CASE WHEN`
- Conditional Aggregation
- `GROUP BY`
- Positive and Negative Value Transformation

---

## Key Takeaways

- `CASE WHEN` can transform values before aggregation.
- Deposits are represented as positive values, while withdrawals are represented as negative values.
- Conditional aggregation enables multiple business rules to be handled in a single query.
- `SUM()` computes the final account balance by adding all transformed transaction amounts.
- `GROUP BY` ensures that the balance is calculated independently for each account.