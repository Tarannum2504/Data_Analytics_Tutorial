# SQL Joins

## Learning Objectives

After completing this topic, you should be able to:

- Understand different types of SQL JOINs.
- Choose the appropriate JOIN for a problem.
- Join multiple tables.
- Understand how SQL executes multiple JOINs.
- Solve common interview and Data Analytics JOIN problems.

---

# What is a JOIN?

A **JOIN** combines rows from two or more tables using a related column.

---

# Types of JOINs

| JOIN | Returns |
|------|---------|
| INNER JOIN | Only matching rows |
| LEFT JOIN | All Left + Matching Right |
| RIGHT JOIN | All Right + Matching Left |
| FULL JOIN | All rows from both tables |
| CROSS JOIN | Every possible combination |
| SELF JOIN | Table joined with itself |
| NATURAL JOIN | SQL automatically finds common columns |

---

# Sample Dataset

### Employee

| emp_id | emp_name | dept_id | manager_id |
|--------|----------|----------|------------|
|1|Ravi|101|1|
|2|Aman|102|1|
|3|Priya|103|2|

### Department

| dept_id | dept_name |
|----------|-----------|
|101|IT|
|102|HR|
|104|Finance|

---

# INNER JOIN

### Meaning

Returns **only matching rows** from both tables.

### Syntax

```sql
SELECT *
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

### Example

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
INNER JOIN department d
ON e.dept_id = d.dept_id;
```

### Important Notes

- Returns matching rows only.
- Unmatched rows are discarded.
- Most commonly used when related records are required.

### Interview Tip

> Think of INNER JOIN as the **intersection** of two tables.

---

# LEFT JOIN

### Meaning

Returns:

- All rows from Left table.
- Matching rows from Right table.

Missing matches become **NULL**.

### Syntax

```sql
SELECT *
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

### Example

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
LEFT JOIN department d
ON e.dept_id=d.dept_id;
```

### Important Notes

- Preserves every row from the Left table.
- Missing matches become NULL.
- Most frequently used JOIN in Data Analytics.
- Useful for finding missing records.

Example:

```sql
SELECT c.customer_id
FROM customers c
LEFT JOIN orders o
ON c.customer_id=o.customer_id
WHERE o.customer_id IS NULL;
```

### Interview Tip

```text
LEFT JOIN

=

INNER JOIN

+

Remaining Left Records
```
---

# RIGHT JOIN

### Meaning

Returns:

- All rows from the Right table.
- Matching rows from the Left table.

Missing matches become **NULL**.

### Syntax

```sql
SELECT *
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

### Example

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
RIGHT JOIN department d
ON e.dept_id = d.dept_id;
```

### Important Notes

- Preserves every row from the Right table.
- Missing matches become NULL.
- Can usually be rewritten as a LEFT JOIN by swapping table positions.

### Interview Tip

```text
RIGHT JOIN

=

INNER JOIN

+

Remaining Right Records
```

---

# FULL JOIN

### Meaning

Returns:

- Matching rows.
- Remaining rows from Left table.
- Remaining rows from Right table.

Missing values are filled with **NULL**.

### Syntax

```sql
SELECT *
FROM table1
FULL JOIN table2
ON table1.column = table2.column;
```

### Example

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
FULL JOIN department d
ON e.dept_id = d.dept_id;
```

### Important Notes

- Returns every row from both tables.
- Useful for identifying unmatched records from either table.

### Interview Tip

```text
FULL JOIN

=

INNER JOIN

+

Remaining Left Records

+

Remaining Right Records
```

---

# OUTER JOIN

The keyword **OUTER** is optional.

These statements are identical.

```sql
LEFT JOIN
=
LEFT OUTER JOIN
```

```sql
RIGHT JOIN
=
RIGHT OUTER JOIN
```

```sql
FULL JOIN
=
FULL OUTER JOIN
```

---

# SELF JOIN

### Meaning

A table joined with itself.

Used when rows within the same table are related.

### Syntax

```sql
SELECT ...
FROM employee e
LEFT JOIN employee m
ON e.manager_id = m.emp_id;
```

### Common Use Cases

- Employee → Manager
- Category → Parent Category
- Organization Hierarchy

### Important Notes

- Aliases are mandatory.
- The same table acts as two logical tables.

### Interview Tip

```text
SELF JOIN

=

Table joins with itself.
```

---

# Multiple JOINs

A query can contain multiple JOINs.

Example:

```sql
SELECT e.emp_name,
       d.dept_name,
       m.manager_name,
       p.project_name
FROM employee e
LEFT JOIN department d
ON e.dept_id = d.dept_id
INNER JOIN manager m
ON e.manager_id = m.manager_id
LEFT JOIN projects p
ON e.emp_id = p.team_member_id;
```

---

## JOIN Execution Flow

SQL executes JOINs from **top to bottom**.

Each JOIN works on the result produced by the previous JOIN.

```text
Employee

↓

LEFT JOIN Department

↓

INNER JOIN Manager

↓

LEFT JOIN Projects
```

---

# ⭐ Important Rule (TechTFQ)

When SQL encounters a **LEFT JOIN**, the **entire result produced before that LEFT JOIN** becomes the left side.

It **does not** consider only the table written immediately before the LEFT JOIN.

```text
Employee

LEFT JOIN Department

↓

INNER JOIN Manager

↓

Complete Result

↓

LEFT JOIN Projects
```

### Remember

```text
Every JOIN operates on the CURRENT RESULT SET,

not on the previous table.
```

This is one of the most commonly misunderstood concepts in complex JOIN queries.

---

# CROSS JOIN

### Meaning

A **CROSS JOIN** returns the **Cartesian Product** of two tables.

Every row from the first table is combined with every row from the second table.

### Syntax

```sql
SELECT *
FROM table1
CROSS JOIN table2;
```

> **Note:** `CROSS JOIN` does **not** require an `ON` clause.

### Example

```sql
SELECT *
FROM employee
CROSS JOIN department;
```

### Important Notes

- Returns **m × n** rows.
- No common column is required.
- Every row is paired with every other row.

### Practical Use Case

Use a CROSS JOIN when two tables have **no common key**, but you still need to combine their data.

Example:

- Employee table
- Company table (only one row containing company information)

```sql
SELECT *
FROM employee
CROSS JOIN company;
```

Every employee receives the company information.

### Interview Tip

```text
CROSS JOIN

=

Cartesian Product

=

Every Row × Every Row
```

---

# NATURAL JOIN

### Meaning

A **NATURAL JOIN** automatically joins two tables using columns that have the **same name**.

Unlike an INNER JOIN, no `ON` clause is required.

### Syntax

```sql
SELECT *
FROM table1
NATURAL JOIN table2;
```

### How it Works

SQL automatically:

1. Finds columns having the same name.
2. Uses those columns as the join condition.
3. Performs an INNER JOIN.

Example

Employee

```
emp_id
dept_id
```

Department

```
dept_id
dept_name
```

SQL automatically joins using:

```text
dept_id
```

---

### If No Common Column Exists

If both tables have **no common column names**, the result behaves like a **CROSS JOIN (Cartesian Product)**.

---

### Why NATURAL JOIN is Discouraged

Avoid using NATURAL JOIN because:

- SQL automatically chooses the join columns.
- You lose control over the query.
- Schema changes can silently change query results.
- Difficult to debug and maintain.

Prefer:

```sql
INNER JOIN ...
ON ...
```

---

# JOIN Comparison

| JOIN | Returns |
|------|---------|
| INNER | Matching rows only |
| LEFT | All Left + Matching Right |
| RIGHT | All Right + Matching Left |
| FULL | All rows from both tables |
| CROSS | Every possible combination |
| SELF | Table joined with itself |
| NATURAL | SQL automatically joins common columns |

---

# Common Mistakes

### Forgetting the ON clause

All joins except:

- CROSS JOIN
- NATURAL JOIN

require an `ON` condition.

---

### Confusing LEFT and RIGHT JOIN

Remember:

```text
LEFT JOIN

Keeps

LEFT Table
```

```text
RIGHT JOIN

Keeps

RIGHT Table
```

---

### Assuming JOIN works only between two tables

A query may contain multiple joins.

Each JOIN works on the **current result set**, not on the previous table.

---

### Using NATURAL JOIN in production

Avoid NATURAL JOIN.

Always prefer explicit join conditions.

---

# Best Practices

- Use meaningful table aliases.
- Prefer LEFT JOIN over RIGHT JOIN.
- Write explicit join conditions.
- Use CROSS JOIN only when required.
- Avoid NATURAL JOIN in production code.
- Understand JOIN execution order before writing complex queries.

---

# Quick Revision

```text
INNER JOIN
=
Matching Rows

LEFT JOIN
=
INNER JOIN
+
Remaining Left Rows

RIGHT JOIN
=
INNER JOIN
+
Remaining Right Rows

FULL JOIN
=
INNER JOIN
+
Remaining Left Rows
+
Remaining Right Rows

CROSS JOIN
=
Cartesian Product

SELF JOIN
=
Table joins with itself

NATURAL JOIN
=
SQL automatically finds common columns
```

---

# Interview Questions

### Q1. Difference between INNER JOIN and LEFT JOIN?

INNER JOIN returns only matching rows.

LEFT JOIN returns all rows from the left table and matching rows from the right table.

---

### Q2. Difference between LEFT JOIN and RIGHT JOIN?

LEFT JOIN preserves the left table.

RIGHT JOIN preserves the right table.

---

### Q3. Difference between FULL JOIN and CROSS JOIN?

FULL JOIN returns matching and non-matching rows.

CROSS JOIN returns every possible combination.

---

### Q4. Why should NATURAL JOIN generally be avoided?

Because SQL automatically determines the join condition, making queries harder to control and maintain.

---

### Q5. What is the Cartesian Product?

If table A has **m** rows and table B has **n** rows:

```text
Result = m × n rows
```

---

# Revision Checklist

- [ ] Understand all JOIN types.
- [ ] Know when to use each JOIN.
- [ ] Understand JOIN execution order.
- [ ] Remember the LEFT JOIN scope rule.
- [ ] Practice SELF JOIN questions.
- [ ] Understand CROSS JOIN and Cartesian Product.
- [ ] Know why NATURAL JOIN is discouraged.
- [ ] Practice 15–20 JOIN problems.

---

# Topics Covered

- ✅ INNER JOIN
- ✅ LEFT JOIN
- ✅ RIGHT JOIN
- ✅ FULL JOIN
- ✅ OUTER JOIN
- ✅ SELF JOIN
- ✅ CROSS JOIN
- ✅ NATURAL JOIN
- ✅ Multiple JOINs
- ✅ JOIN Execution Order
- ✅ LEFT JOIN Scope
- ✅ Cartesian Product
- ✅ Common Mistakes
- ✅ Best Practices
- ✅ Interview Questions