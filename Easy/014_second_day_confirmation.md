# Second Day Confirmation

**Platform:** DataLemur  
**Company:** TikTok  
**Difficulty:** Easy  

---

## Problem

Find the **user IDs** of users who:

- Did **not** confirm their account on the first day.
- Confirmed their account on the **second day** after signing up.

Return:

- `user_id`

---

## Approach

- Join the `emails` and `texts` tables using `email_id`.
- Filter only records where the signup action is **Confirmed**.
- Compare the confirmation date with the signup date.
- Select users whose confirmation happened exactly **one day after signup**.

---

## SQL Solution

```sql
SELECT
    e.user_id
FROM emails e
JOIN texts t
    ON e.email_id = t.email_id
WHERE t.action_date = e.signup_date + INTERVAL '1 DAY'
    AND t.signup_action = 'Confirmed';
```

---

# Query Explanation

## Step 1: Join the Tables

```sql
FROM emails e
JOIN texts t
    ON e.email_id = t.email_id
```

The `emails` table contains user signup information, while the `texts` table stores confirmation actions.

Joining on `email_id` connects each user with their corresponding confirmation record.

Example:

**emails**

| email_id | user_id | signup_date |
|---------:|--------:|------------|
|101|1|2022-06-01|
|102|2|2022-06-05|

**texts**

| email_id | signup_action | action_date |
|---------:|---------------|------------|
|101|Confirmed|2022-06-02|
|102|Confirmed|2022-06-05|

---

## Step 2: Filter Confirmed Accounts

```sql
t.signup_action = 'Confirmed'
```

Only users who successfully confirmed their accounts are considered.

Example:

| signup_action | Included |
|--------------|:--------:|
|Confirmed|✅|
|Not Confirmed|❌|

---

## Step 3: Check for Second-Day Confirmation

```sql
t.action_date = e.signup_date + INTERVAL '1 DAY'
```

Adds **one day** to the signup date.

Only users whose confirmation occurred exactly one day later satisfy the condition.

Example:

| Signup Date | Confirmation Date | Match |
|-------------|-------------------|:----:|
|2022-06-01|2022-06-02|✅|
|2022-06-01|2022-06-01|❌|
|2022-06-01|2022-06-03|❌|

---

## Step 4: Return User IDs

```sql
SELECT
    e.user_id
```

Returns the IDs of users who confirmed on the second day.

---

## Alternative Approaches

### 1. Using `INTERVAL` *(Recommended for PostgreSQL)*

```sql
SELECT
    e.user_id
FROM emails e
JOIN texts t
    ON e.email_id = t.email_id
WHERE t.action_date = e.signup_date + INTERVAL '1 DAY'
    AND t.signup_action = 'Confirmed';
```

**Database Support:** PostgreSQL

**Why use it?**

- Clean and readable.
- Standard way to perform date arithmetic in PostgreSQL.

---

### 2. Using Date Subtraction

```sql
SELECT
    e.user_id
FROM emails e
JOIN texts t
    ON e.email_id = t.email_id
WHERE t.action_date - e.signup_date = 1
    AND t.signup_action = 'Confirmed';
```

**Database Support:** PostgreSQL

**Why use it?**

- Directly compares the number of days between two dates.
- Useful when calculating day differences.

---

### 3. Using `DATE_ADD()` *(MySQL)*

```sql
SELECT
    e.user_id
FROM emails e
JOIN texts t
    ON e.email_id = t.email_id
WHERE t.action_date = DATE_ADD(e.signup_date, INTERVAL 1 DAY)
    AND t.signup_action = 'Confirmed';
```

**Database Support:** MySQL

**Why use it?**

- Standard approach for date arithmetic in MySQL.

---

## Understanding `JOIN`

An `INNER JOIN` returns only matching rows from both tables.

Syntax:

```sql
SELECT ...
FROM table1
JOIN table2
    ON table1.column = table2.column;
```

In this problem:

```sql
ON e.email_id = t.email_id
```

connects every signup with its confirmation record.

---

## Understanding `INTERVAL`

`INTERVAL` is used to add or subtract a specific amount of time.

Syntax:

```sql
date + INTERVAL '1 DAY'
```

Example:

| Signup Date | Result |
|-------------|--------|
|2022-01-10|2022-01-11|

Common intervals:

```sql
INTERVAL '1 DAY'
INTERVAL '7 DAYS'
INTERVAL '1 MONTH'
INTERVAL '1 YEAR'
```

---

## Understanding Date Arithmetic

SQL allows arithmetic operations on dates.

Example:

```sql
signup_date + INTERVAL '1 DAY'
```

adds one day to a date.

Similarly,

```sql
action_date - signup_date
```

returns the number of days between two dates in PostgreSQL.

---

## Understanding `WHERE`

The `WHERE` clause filters rows before returning the final result.

Here, it applies two conditions:

- The user confirmed the account.
- The confirmation occurred exactly one day after signup.

Only rows satisfying **both** conditions are returned.

---

## Concepts

- INNER JOIN
- Date Arithmetic
- `INTERVAL`
- WHERE
- Equality Comparison
- Filtering
- Date Functions

---

## Key Takeaways

- `JOIN` combines related information stored in different tables.
- `INTERVAL` is PostgreSQL's standard method for adding or subtracting time.
- Date arithmetic makes it easy to compare events occurring a fixed number of days apart.
- Multiple filtering conditions can be combined using `AND`.
- Problems involving dates often require understanding how SQL handles date calculations and intervals.