# Teams Power Users

**Platform:** DataLemur 
**Company:** Microsoft 
**Difficulty:** Easy 

---

## Problem

Identify the **top 2 Microsoft Teams users** who sent the highest number of messages during **August 2022**.

The output should include:

* `sender_id`
* Total number of messages sent (`count_messages`)

Sort the results in **descending order** of the message count.

---

## Approach

* Filter messages sent in **August 2022**.
* Group messages by `sender_id`.
* Count the number of messages sent by each user.
* Sort the users by message count in descending order.
* Return only the top **2** users.

---

## SQL Solution

```sql
SELECT
    sender_id,
    COUNT(message_id) AS count_messages
FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = 8
  AND EXTRACT(YEAR FROM sent_date) = 2022
GROUP BY sender_id
ORDER BY count_messages DESC
LIMIT 2;
```

---

# Query Explanation

## Step 1: Filter Messages from August 2022

```sql
WHERE EXTRACT(MONTH FROM sent_date) = 8
  AND EXTRACT(YEAR FROM sent_date) = 2022
```

Only messages sent during **August 2022** are included.

Example:

| sent_date  | Included |
| ---------- | :------: |
| 2022-08-05 |     ✅    |
| 2022-08-21 |     ✅    |
| 2022-07-30 |     ❌    |
| 2023-08-10 |     ❌    |

---

## Step 2: Group by Sender

```sql
GROUP BY sender_id
```

All messages sent by the same user are grouped together.

Example:

| sender_id | message_id |
| --------- | ---------- |
| 101       | 1          |
| 101       | 2          |
| 101       | 3          |
| 205       | 4          |
| 205       | 5          |

Groups:

**Sender 101**

* Message 1
* Message 2
* Message 3

**Sender 205**

* Message 4
* Message 5

---

## Step 3: Count Messages

```sql
COUNT(message_id)
```

Counts the total number of messages in each sender's group.

Example:

| sender_id | Messages |
| --------- | -------: |
| 101       |        3 |
| 205       |        2 |
| 301       |        5 |

---

## Step 4: Sort by Message Count

```sql
ORDER BY count_messages DESC
```

Orders the users from the highest message count to the lowest.

Example:

| sender_id | count_messages |
| --------- | -------------: |
| 301       |              5 |
| 101       |              3 |
| 205       |              2 |

---

## Step 5: Return Top Two Users

```sql
LIMIT 2
```

Returns only the first two rows after sorting.

Result:

| sender_id | count_messages |
| --------- | -------------: |
| 301       |              5 |
| 101       |              3 |

---

## Alternative Approaches

### 1. Using `EXTRACT()` + `LIMIT` *(Recommended)*

```sql
SELECT
    sender_id,
    COUNT(message_id) AS count_messages
FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = 8
  AND EXTRACT(YEAR FROM sent_date) = 2022
GROUP BY sender_id
ORDER BY count_messages DESC
LIMIT 2;
```

**Database Support:** PostgreSQL, Oracle

**Why use it?**

* Simple and concise.
* Most common interview solution.
* Easy to understand.

---

### 2. Using Date Range Filtering *(Recommended for Production)*

```sql
SELECT
    sender_id,
    COUNT(message_id) AS count_messages
FROM messages
WHERE sent_date >= '2022-08-01'
  AND sent_date < '2022-09-01'
GROUP BY sender_id
ORDER BY count_messages DESC
LIMIT 2;
```

**Database Support:** All major SQL databases

**Why use it?**

* More index-friendly.
* Avoids applying functions to the date column.
* Generally performs better on large tables.

---

### 3. Using `FETCH FIRST`

```sql
SELECT
    sender_id,
    COUNT(message_id) AS count_messages
FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = 8
  AND EXTRACT(YEAR FROM sent_date) = 2022
GROUP BY sender_id
ORDER BY count_messages DESC
FETCH FIRST 2 ROWS ONLY;
```

**Database Support:** PostgreSQL, Oracle, DB2

**Why use it?**

* ANSI SQL standard.
* More portable across SQL databases than `LIMIT`.

---

### 4. Using `TOP`

```sql
SELECT TOP 2
    sender_id,
    COUNT(message_id) AS count_messages
FROM messages
WHERE MONTH(sent_date) = 8
  AND YEAR(sent_date) = 2022
GROUP BY sender_id
ORDER BY count_messages DESC;
```

**Database Support:** SQL Server

**Why use it?**

* Standard approach in SQL Server.
* Frequently used in Microsoft interview questions.

---

## Understanding `LIMIT`

`LIMIT` restricts the number of rows returned by a query.

Syntax:

```sql
LIMIT n
```

Example:

```sql
SELECT *
FROM employees
LIMIT 5;
```

Returns only the first **5 rows**.

---

## Understanding `ORDER BY`

`ORDER BY` sorts the result set.

Syntax:

```sql
ORDER BY column ASC
```

or

```sql
ORDER BY column DESC
```

* `ASC` → Ascending order *(default)*
* `DESC` → Descending order

Example:

| Score |
| ----: |
|    98 |
|    75 |
|    82 |

Using

```sql
ORDER BY Score DESC
```

Result:

98 → 82 → 75

---

## Understanding `EXTRACT()`

The `EXTRACT()` function retrieves a specific component from a `DATE` or `TIMESTAMP`.

Syntax:

```sql
EXTRACT(field FROM source)
```

Examples:

```sql
EXTRACT(YEAR FROM sent_date)
```

Returns:

```text
2022
```

```sql
EXTRACT(MONTH FROM sent_date)
```

Returns:

```text
8
```

---

## Understanding `COUNT()`

`COUNT()` returns the number of rows in each group.

Syntax:

```sql
COUNT(column_name)
```

Example:

| sender_id |
| --------- |
| 101       |
| 101       |
| 101       |
| 205       |

Result:

| sender_id | COUNT |
| --------- | ----: |
| 101       |     3 |
| 205       |     1 |

---

## Concepts

* Aggregate Functions (`COUNT`)
* Date Functions (`EXTRACT`)
* `GROUP BY`
* `ORDER BY`
* `LIMIT`
* Date Range Filtering
* Top-N Queries

---

## Key Takeaways

* `GROUP BY` groups rows before aggregate functions such as `COUNT()` are calculated.
* `COUNT()` determines the number of messages sent by each user.
* `ORDER BY DESC` ranks users from the highest to the lowest message count.
* `LIMIT` is used to return only the top **N** rows after sorting.
* For `TIMESTAMP` columns, date range filtering (`>=` and `<`) is generally preferred over extracting the month and year because it allows better index utilization.
* SQL databases use different syntax for Top-N queries: `LIMIT` (PostgreSQL/MySQL), `FETCH FIRST` (ANSI SQL), and `TOP` (SQL Server).
