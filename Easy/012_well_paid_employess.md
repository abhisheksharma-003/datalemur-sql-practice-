# Well Paid Employees

**Platform:** DataLemur 
**Company:** FAANG 
**Difficulty:** Easy 

---

## Problem

Find all employees whose **salary is greater than their direct manager's salary**.

Return:

- `employee_id`
- `employee_name`

---

## Approach

- Perform a **self join** on the `employee` table.
- Join each employee with their corresponding manager using `manager_id`.
- Compare the employee's salary with their manager's salary.
- Return employees earning more than their managers.

---

## SQL Solution

```sql
SELECT
    e2.employee_id AS employee_id,
    e2.name AS employee_name
FROM employee AS e1
INNER JOIN employee AS e2
    ON e1.employee_id = e2.manager_id
WHERE e1.salary < e2.salary;
```

---

# Query Explanation

## Step 1: Perform a Self Join

```sql
FROM employee AS e1
INNER JOIN employee AS e2
    ON e1.employee_id = e2.manager_id
```

Since managers are also employees, we join the **employee** table with itself.

Here:

- `e1` → Manager
- `e2` → Employee

The join condition:

```sql
e1.employee_id = e2.manager_id
```

matches every employee with their direct manager.

Example:

| employee_id | manager_id |
|------------:|-----------:|
|1|6|
|2|7|
|3|8|

Managers:

| employee_id |
|------------:|
|6|
|7|
|8|

The join creates:

| Manager | Employee |
|---------|----------|
|6|1|
|7|2|
|8|3|

---

## Step 2: Compare Salaries

```sql
WHERE e1.salary < e2.salary
```

Checks whether the employee earns more than their manager.

Example:

| Manager Salary | Employee Salary | Result |
|---------------:|----------------:|:------:|
|7000|9000|✅|
|10000|8500|❌|

---

## Step 3: Return Employee Information

```sql
SELECT
    e2.employee_id,
    e2.name
```

Returns only the employee's details, as required.

---

---

# Common Mistakes & Learning Notes

## Mistake 1: Using `employee_salary` in the `WHERE` clause

❌ Incorrect

```sql
WHERE e2.employee_salary > e1.employee_salary
```

### Why is it wrong?

`employee_salary` is **not an actual column** in the `employee` table.

It is a **column alias** created in the `SELECT` statement:

```sql
SELECT
    e2.salary AS employee_salary
```

SQL executes the query in this order:

1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT ← aliases are created here
7. ORDER BY

Since the `WHERE` clause executes **before** the `SELECT` clause, PostgreSQL does not yet know what `employee_salary` is.

### Correct

```sql
WHERE e2.salary > e1.salary
```

Always use the **original column names** inside the `WHERE` clause.

---

## Mistake 2: Understanding the Self Join

Initially, it was confusing which alias represented the **manager** and which represented the **employee**.

The correct relationship is:

```
Manager (employee_id)
        │
        │
Employee (manager_id)
```

Therefore, the join should be:

```sql
e1.employee_id = e2.manager_id
```

Where:

- `e1` → Manager
- `e2` → Employee

---

## Mistake 3: Using Generic Aliases

Using aliases like:

```sql
e1
e2
```

works perfectly, but they become difficult to understand in self joins.

A more descriptive version is:

```sql
FROM employee AS manager
INNER JOIN employee AS employee
    ON manager.employee_id = employee.manager_id
```

This immediately makes the relationship obvious and is often preferred in production code.

---

## Lesson Learned

When writing SQL involving self joins:

- First identify what each table alias represents.
- Draw the relationship between the two tables (or the two roles of the same table).
- Join using the primary key and foreign key relationship.
- Remember that **column aliases cannot be referenced in the `WHERE` clause** because they are created later in the query execution order.

---

## Alternative Approaches

### 1. Using `INNER JOIN` *(Recommended)*

```sql
SELECT
    e2.employee_id,
    e2.name
FROM employee e1
INNER JOIN employee e2
    ON e1.employee_id = e2.manager_id
WHERE e1.salary < e2.salary;
```

**Database Support:** All SQL databases

**Why use it?**

- Most readable.
- Most efficient.
- Standard interview solution.

---

### 2. Using Table Aliases with Descriptive Names

```sql
SELECT
    employee.employee_id,
    employee.name
FROM employee AS manager
INNER JOIN employee AS employee
    ON manager.employee_id = employee.manager_id
WHERE employee.salary > manager.salary;
```

**Database Support:** All SQL databases

**Why use it?**

- Easier to understand than generic aliases (`e1`, `e2`).
- Recommended for complex self joins.

---

### 3. Using a Common Table Expression (CTE)

```sql
WITH employee_manager AS (
    SELECT
        manager.salary AS manager_salary,
        employee.employee_id,
        employee.name,
        employee.salary
    FROM employee AS manager
    INNER JOIN employee AS employee
        ON manager.employee_id = employee.manager_id
)

SELECT
    employee_id,
    name AS employee_name
FROM employee_manager
WHERE salary > manager_salary;
```

**Database Support:** PostgreSQL, SQL Server, MySQL 8+, Oracle

**Why use it?**

- Helpful when additional processing is required.
- Makes large queries easier to read.

---

## Understanding Self Join

A **Self Join** joins a table with itself.

It is commonly used for:

- Employee → Manager relationships
- Parent → Child relationships
- Category hierarchies
- Organizational charts

Syntax:

```sql
SELECT ...
FROM table_name t1
JOIN table_name t2
ON t1.column = t2.column;
```

Although the same table is used twice, each alias represents a different role.

---

## Understanding Table Aliases

Aliases give temporary names to tables.

Example:

```sql
FROM employee AS manager
INNER JOIN employee AS employee
```

Instead of writing the full table name repeatedly, aliases make queries shorter and easier to read.

---

## Understanding `INNER JOIN`

An `INNER JOIN` returns only matching rows from both tables.

Example:

Employee

| employee_id | manager_id |
|------------:|-----------:|
|1|6|
|2|7|

Manager

| employee_id |
|------------:|
|6|
|7|

Result:

| Employee | Manager |
|----------|---------|
|1|6|
|2|7|

Rows without matching managers are excluded.

---

## Understanding `WHERE`

The `WHERE` clause filters rows before the final result is returned.

Example:

```sql
WHERE employee.salary > manager.salary
```

Only employees earning more than their managers remain in the output.

---

## Concepts

- Self Join
- INNER JOIN
- Table Aliases
- WHERE
- Comparison Operators (`>`, `<`)
- Employee–Manager Relationships

---

## Key Takeaways

- A self join allows a table to be joined with itself using different aliases.
- Table aliases help distinguish different roles of the same table during a self join.
- `INNER JOIN` returns only rows with matching managers and employees.
- The `WHERE` clause compares employee and manager salaries after the join.
- Self joins are one of the most common SQL interview topics for hierarchical data such as employees, managers, categories, and organizational structures.