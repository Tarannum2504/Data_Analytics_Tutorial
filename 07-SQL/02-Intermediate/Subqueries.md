# Subqueries

## Learning Objectives

After completing this topic, you should be able to:

- Understand what a subquery is.
- Understand SQL execution order with subqueries.
- Differentiate between Scalar, Multiple Row, Correlated, and Nested Subqueries.
- Use subqueries with different SQL clauses.
- Understand where EXISTS and NOT EXISTS are used.
- Solve common interview problems using subqueries.

---

# What is a Subquery?

A **Subquery** (also called an **Inner Query** or **Nested Query**) is a SQL query written inside another SQL query.

The query that contains the subquery is called the **Outer Query (Main Query)**.

The result returned by the subquery is used by the outer query to produce the final output.

### Terminology

- **Outer Query (Main Query):** The primary query.
- **Inner Query (Subquery):** The query enclosed inside parentheses.

### General Syntax

```sql
SELECT column_name
FROM table_name
WHERE column_name OPERATOR
(
    SELECT ...
);
```

---

# SQL Execution Order

SQL executes a normal subquery in the following order:

```
Outer Query

↓

Execute Subquery

↓

Subquery returns result

↓

Outer Query uses the returned result

↓

Final Output
```

### Example

```sql
SELECT *
FROM employee
WHERE salary >
(
    SELECT AVG(salary)
    FROM employee
);
```

### Execution

**Step 1**

```sql
SELECT AVG(salary)
FROM employee;
```

Result

```
5791.66
```

**Step 2**

Outer query becomes

```sql
SELECT *
FROM employee
WHERE salary > 5791.66;
```

**Step 3**

Return all employees whose salary is greater than the average salary.

> **Note**
>
> SQL executes the subquery first unless it is a **Correlated Subquery**.

---

# Types of Subqueries

SQL provides four major types of subqueries:

1. Scalar Subquery
2. Multiple Row Subquery
3. Correlated Subquery
4. Nested Subquery

---

# Scalar Subquery

## Definition

A **Scalar Subquery** returns **exactly one row and one column**.

Since only one value is returned, it can be used with comparison operators like:

- =
- >
- <
- >=
- <=
- <>

### Example

```sql
SELECT AVG(salary)
FROM employee;
```

Output

```
5791.66
```

This single value is then used by the outer query.

```sql
SELECT *
FROM employee
WHERE salary >
(
    SELECT AVG(salary)
    FROM employee
);
```

### Alternative using JOIN

A Scalar Subquery can also be rewritten using a JOIN.

```sql
SELECT e.*
FROM employee e
JOIN
(
    SELECT AVG(salary) AS avg_salary
    FROM employee
) avg_sal
ON e.salary > avg_sal.avg_salary;
```

### Key Points

- Returns only one value.
- Executed only once.
- Commonly used with aggregate functions like:
  - AVG()
  - MAX()
  - MIN()
  - SUM()
  - COUNT()

---

# Multiple Row Subquery

## Definition

A **Multiple Row Subquery** returns more than one row.

It may return:

- One column and multiple rows.
- Multiple columns and multiple rows.

Since multiple values are returned, operators like `IN`, `ANY`, and `ALL` are commonly used.

### Example

Find the highest-paid employee from each department.

```sql
SELECT *
FROM employee
WHERE (dept_name, salary) IN
(
    SELECT dept_name,
           MAX(salary)
    FROM employee
    GROUP BY dept_name
);
```

### Understanding Tuple Comparison

Instead of comparing a single column:

```sql
salary IN (...)
```

we compare an entire row:

```sql
(dept_name, salary)
IN (...)
```

SQL compares both values together.

Example:

```
(Finance, 6500)

(HR, 8000)

(Admin, 5000)
```

Only matching tuples are returned.

---

## Example using NOT IN

Find departments that have no employees.

```sql
SELECT *
FROM department
WHERE dept_name NOT IN
(
    SELECT DISTINCT dept_name
    FROM employee
);
```

### Key Points

- Returns multiple rows.
- Usually used with:
  - IN
  - NOT IN
  - ANY
  - ALL
- Can return multiple columns using tuple comparison.
- Executes only once.

---

# Correlated Subquery

## Definition

A **Correlated Subquery** is a subquery that depends on the outer query for its execution.

Unlike a normal subquery, it **cannot execute independently** because it references one or more columns from the outer query.

> **Simple Definition**
>
> A Correlated Subquery is a subquery that is related to the outer query.

---

## Why do we need Correlated Subqueries?

A normal subquery calculates its result only once.

However, some problems require a different calculation for every row.

### Example

Find employees whose salary is greater than the **average salary of their own department**.

Since each department has a different average salary, the subquery must execute separately for every employee.

---

## Example

```sql
SELECT *
FROM employee e1
WHERE salary >
(
    SELECT AVG(salary)
    FROM employee e2
    WHERE e2.dept_name = e1.dept_name
);
```

---

## Understanding the Query

The condition

```sql
WHERE e2.dept_name = e1.dept_name
```

references `e1.dept_name`, which belongs to the outer query.

Because the inner query depends on the outer query, it is called a **Correlated Subquery**.

---

## Execution Flow

Unlike a normal subquery, SQL does **not** execute the inner query only once.

Instead, the following process repeats for every row.

```
Read one row from Outer Query

↓

Execute Correlated Subquery

↓

Return Result

↓

Evaluate Condition

↓

Move to Next Row
```

---

## Performance Note

Since the subquery executes once for every row of the outer query, correlated subqueries are generally slower than normal subqueries.

Whenever possible, check whether the same problem can be solved using:

- JOIN
- CTE
- Window Functions

These approaches are often easier to optimize.

---

## Key Points

- Depends on the outer query.
- Cannot execute independently.
- Executes once for every outer row.
- Frequently used with `EXISTS` and `NOT EXISTS`.

---

# Nested Subquery

## Definition

A **Nested Subquery** is a subquery written inside another subquery.

```
Outer Query
    ↓
Subquery
    ↓
Another Subquery
```

---

## When is it Used?

Nested subqueries are useful when solving a problem requires multiple levels of calculation.

Example:

1. Calculate total sales of every store.
2. Calculate the average of those totals.
3. Compare each store's total sales with the average.

Since one calculation depends on another, nested subqueries are required.

---

## Example

```sql
SELECT *
FROM
(
    SELECT store_name,
           SUM(price) AS total_sales
    FROM sales
    GROUP BY store_name
) sales
JOIN
(
    SELECT AVG(total_sales) AS avg_sales
    FROM
    (
        SELECT store_name,
               SUM(price) AS total_sales
        FROM sales
        GROUP BY store_name
    ) x
) avg_sales
ON sales.total_sales > avg_sales.avg_sales;
```

---

## Execution Flow

```
Calculate Total Sales

↓

Calculate Average of Total Sales

↓

Compare Both Results

↓

Return Matching Rows
```

---

## Note

Nested subqueries can become difficult to read when the same subquery is repeated multiple times.

A **Common Table Expression (CTE)** provides a cleaner and more readable alternative.

> 📖 CTEs are covered separately in **CTEs.md**.

---

## Key Points

- A nested subquery is simply a subquery inside another subquery.
- Used when one calculation depends on another.
- Deep nesting reduces readability.
- Repeated nested subqueries are often replaced with CTEs.

---

# Correlated Subquery

## Definition

A **Correlated Subquery** is a subquery that depends on the outer query for its execution.

Unlike a normal subquery, it **cannot execute independently** because it references one or more columns from the outer query.

> **Simple Definition**
>
> A Correlated Subquery is a subquery that is related to the outer query.

---

## Why do we need Correlated Subqueries?

A normal subquery calculates its result only once.

However, some problems require a different calculation for every row.

### Example

Find employees whose salary is greater than the **average salary of their own department**.

Since each department has a different average salary, the subquery must execute separately for every employee.

---

## Example

```sql
SELECT *
FROM employee e1
WHERE salary >
(
    SELECT AVG(salary)
    FROM employee e2
    WHERE e2.dept_name = e1.dept_name
);
```

---

## Understanding the Query

The condition

```sql
WHERE e2.dept_name = e1.dept_name
```

references `e1.dept_name`, which belongs to the outer query.

Because the inner query depends on the outer query, it is called a **Correlated Subquery**.

---

## Execution Flow

Unlike a normal subquery, SQL does **not** execute the inner query only once.

Instead, the following process repeats for every row.

```
Read one row from Outer Query

↓

Execute Correlated Subquery

↓

Return Result

↓

Evaluate Condition

↓

Move to Next Row
```

---

## Performance Note

Since the subquery executes once for every row of the outer query, correlated subqueries are generally slower than normal subqueries.

Whenever possible, check whether the same problem can be solved using:

- JOIN
- CTE
- Window Functions

These approaches are often easier to optimize.

---

## Key Points

- Depends on the outer query.
- Cannot execute independently.
- Executes once for every outer row.
- Frequently used with `EXISTS` and `NOT EXISTS`.

---

# Nested Subquery

## Definition

A **Nested Subquery** is a subquery written inside another subquery.

```
Outer Query
    ↓
Subquery
    ↓
Another Subquery
```

---

## When is it Used?

Nested subqueries are useful when solving a problem requires multiple levels of calculation.

Example:

1. Calculate total sales of every store.
2. Calculate the average of those totals.
3. Compare each store's total sales with the average.

Since one calculation depends on another, nested subqueries are required.

---

## Example

```sql
SELECT *
FROM
(
    SELECT store_name,
           SUM(price) AS total_sales
    FROM sales
    GROUP BY store_name
) sales
JOIN
(
    SELECT AVG(total_sales) AS avg_sales
    FROM
    (
        SELECT store_name,
               SUM(price) AS total_sales
        FROM sales
        GROUP BY store_name
    ) x
) avg_sales
ON sales.total_sales > avg_sales.avg_sales;
```

---

## Execution Flow

```
Calculate Total Sales

↓

Calculate Average of Total Sales

↓

Compare Both Results

↓

Return Matching Rows
```

---

## Note

Nested subqueries can become difficult to read when the same subquery is repeated multiple times.

A **Common Table Expression (CTE)** provides a cleaner and more readable alternative.

> 📖 CTEs are covered separately in **CTEs.md**.

---

## Key Points

- A nested subquery is simply a subquery inside another subquery.
- Used when one calculation depends on another.
- Deep nesting reduces readability.
- Repeated nested subqueries are often replaced with CTEs.

---

# EXISTS vs NOT EXISTS

## EXISTS

The `EXISTS` operator returns **TRUE** if the subquery returns **at least one row**.

It is commonly used to check whether matching records exist.

### Syntax

```sql
SELECT *
FROM table1 t1
WHERE EXISTS
(
    SELECT 1
    FROM table2 t2
    WHERE t2.column = t1.column
);
```

### Example

```sql
SELECT *
FROM employee e
WHERE EXISTS
(
    SELECT 1
    FROM department d
    WHERE d.dept_name = e.dept_name
);
```

**Result**

Returns employees whose department exists in the Department table.

---

## NOT EXISTS

`NOT EXISTS` returns **TRUE** when the subquery returns **no rows**.

It is mainly used to find missing records.

### Syntax

```sql
SELECT *
FROM table1 t1
WHERE NOT EXISTS
(
    SELECT 1
    FROM table2 t2
    WHERE t2.column = t1.column
);
```

### Example

```sql
SELECT *
FROM department d
WHERE NOT EXISTS
(
    SELECT 1
    FROM employee e
    WHERE e.dept_name = d.dept_name
);
```

**Result**

Returns departments that do not have any employees.

---

## Why do we use `SELECT 1`?

Inside `EXISTS` or `NOT EXISTS`, SQL only checks whether at least one row exists.

The selected column values are ignored.

Therefore,

```sql
SELECT 1
```

is commonly used because it clearly indicates that only row existence matters.

---

# Comparison of Subquery Types

| Type | Returns | Execution | Common Operators |
|-------|----------|-----------|------------------|
| Scalar | One row, One column | Executes once | =, >, < |
| Multiple Row | Multiple rows | Executes once | IN, ANY, ALL |
| Correlated | Depends on outer query | Executes for every outer row | EXISTS, NOT EXISTS |
| Nested | Subquery inside another subquery | Depends on nesting level | Any |

---

# Common Mistakes

### Mistake 1

Using `=` when the subquery returns multiple rows.

❌ Incorrect

```sql
WHERE dept_name =
(
    SELECT dept_name
    FROM employee
);
```

✔ Correct

```sql
WHERE dept_name IN
(
    SELECT dept_name
    FROM employee
);
```

---

### Mistake 2

Thinking every subquery executes only once.

Only normal subqueries execute once.

Correlated subqueries execute once for every row processed by the outer query.

---

### Mistake 3

Using correlated subqueries when a JOIN or Window Function would be simpler.

Always check whether the same result can be achieved using:

- JOIN
- CTE
- Window Functions

---

### Mistake 4

Repeating the same subquery multiple times.

Repeated subqueries reduce readability and make maintenance difficult.

Consider using a **CTE** instead.

---

### Mistake 5

Using `NOT IN` when the subquery may contain `NULL` values.

This can produce unexpected results.

Whenever appropriate, prefer `NOT EXISTS`.

---

# Best Practices

- Write meaningful table aliases (`e`, `d`, `eh`, etc.).
- Use Scalar Subqueries only when a single value is expected.
- Use `IN` when multiple values are returned.
- Use `EXISTS` and `NOT EXISTS` for existence checks.
- Prefer `CROSS JOIN` when the same scalar value is needed for every row.
- Avoid deeply nested subqueries whenever possible.
- Replace repeated subqueries with **CTEs** for better readability.

---

# Interview Notes

### What is a Subquery?

A query written inside another SQL query whose result is used by the outer query.

---

### What is the difference between a Scalar and Multiple Row Subquery?

| Scalar | Multiple Row |
|---------|--------------|
| Returns one value | Returns multiple values |
| Uses =, >, < | Uses IN, ANY, ALL |

---

### What is a Correlated Subquery?

A subquery that depends on the outer query and executes once for every outer row.

---

### Why is a Correlated Subquery slower?

Because the inner query executes repeatedly for each row processed by the outer query.

---

### Difference between EXISTS and IN?

- `EXISTS` checks whether matching rows exist.
- `IN` compares values returned by a subquery.

`EXISTS` is generally preferred for existence checks on large datasets.

---

### Why is `SELECT 1` used with EXISTS?

Because `EXISTS` only checks whether a row exists.

The selected column values are ignored.

---

# Revision Checklist

- [ ] Understand SQL execution order.
- [ ] Differentiate between all four types of subqueries.
- [ ] Know where subqueries can be used.
- [ ] Understand EXISTS and NOT EXISTS.
- [ ] Practice Scalar Subqueries.
- [ ] Practice Multiple Row Subqueries.
- [ ] Practice Correlated Subqueries.
- [ ] Practice Nested Subqueries.
- [ ] Solve INSERT, UPDATE and DELETE problems using subqueries.
- [ ] Revise common mistakes before interviews.

---

# Topics Covered

- ✅ Scalar Subquery
- ✅ Multiple Row Subquery
- ✅ Correlated Subquery
- ✅ Nested Subquery
- ✅ Subqueries in SELECT
- ✅ Subqueries in FROM
- ✅ Subqueries in WHERE
- ✅ Subqueries in HAVING
- ✅ INSERT using Subquery
- ✅ UPDATE using Subquery
- ✅ DELETE using Subquery
- ✅ EXISTS
- ✅ NOT EXISTS