# SQL_BASICS_CHEATSHEET.md

> Purpose: A complete SQL Fundamentals revision guide for Data Analytics, SQL Interviews, LeetCode SQL 50, HackerRank SQL, and Projects.

---

# 1. QUERY EXECUTION ORDER ⭐

Most beginners think SQL executes in the order they write it.

We write:

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT;
```

But SQL actually executes in this order:

```text
1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT / OFFSET
```

## Why is this Important?

Understanding execution order helps explain many interview questions.

### Example

```sql
SELECT salary AS sal
FROM employees
WHERE sal > 50000;
```

❌ Error

Why?

Because:

```text
WHERE executes before SELECT.
```

The alias `sal` does not exist yet when WHERE is executed.

---

## Interview Question

Q. Why can't aliases be used in WHERE?

Answer:

Because WHERE executes before SELECT.

---

# 2. SELECT

## Meaning

SELECT is used to retrieve data from one or more tables.

It is the most frequently used SQL statement.

---

## Syntax

```sql
SELECT column_name
FROM table_name;
```

---

## Selecting Multiple Columns

```sql
SELECT employee_name,
       salary,
       department
FROM employees;
```

---

## Selecting All Columns

```sql
SELECT *
FROM employees;
```

### What does * mean?

It means:

```text
Select all columns from the table.
```

---

## Analytics Use Case

Suppose you have:

```text
employee_id
employee_name
salary
department
city
joining_date
```

If you only need salary and department:

```sql
SELECT salary,
       department
FROM employees;
```

This is more efficient than:

```sql
SELECT *
FROM employees;
```

---

## Best Practice

Avoid:

```sql
SELECT *
FROM employees;
```

Prefer:

```sql
SELECT employee_name,
       salary
FROM employees;
```

---

## Common Pitfalls

### Pitfall 1

Using SELECT * unnecessarily.

Reason:

* Slower
* Retrieves unwanted columns
* Consumes memory

---

## Interview Notes

Q. Why avoid SELECT * ?

Answer:

* Better performance
* Improved readability
* Less data transfer

---

# 3. ALIAS (AS)

## Meaning

Alias provides a temporary name to:

* Columns
* Tables

Aliases exist only during query execution.

---

# Column Alias

Without Alias:

```sql
SELECT AVG(salary)
FROM employees;
```

Output:

```text
AVG(salary)
```

Not very readable.

---

With Alias:

```sql
SELECT AVG(salary) AS average_salary
FROM employees;
```

Output:

```text
average_salary
```

Much cleaner.

---

# Table Alias

```sql
SELECT e.employee_name,
       d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id;
```

Here:

```text
e → employees

d → departments
```

---

## Why Use Table Aliases?

Imagine:

```sql
SELECT employees.employee_name,
       departments.department_name
FROM employees
INNER JOIN departments
ON employees.department_id = departments.department_id;
```

Works fine.

But becomes lengthy.

Aliases make queries cleaner.

---

## Common Pitfalls

### Alias in WHERE

```sql
SELECT salary AS sal
FROM employees
WHERE sal > 50000;
```

❌ Invalid

Reason:

WHERE executes before SELECT.

---

## Interview Notes

Alias improves:

* Readability
* Maintainability
* Complex JOIN queries

---

# 4. WHERE

## Meaning

WHERE filters rows based on conditions.

Only rows satisfying the condition are returned.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

---

## Example

Table:

| Name   | Salary |
| ------ | ------ |
| Amit   | 40000  |
| Ravi   | 60000  |
| Anjali | 80000  |

Query:

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

Output:

| Name   | Salary |
| ------ | ------ |
| Ravi   | 60000  |
| Anjali | 80000  |

---

## Analytics Use Cases

Find:

* Employees earning above average
* Customers from a city
* Orders after a date
* Products above a price

---

## Common Pitfalls

Using aggregate functions.

```sql
SELECT *
FROM employees
WHERE AVG(salary) > 50000;
```

❌ Invalid

Use HAVING instead.

---

## Interview Notes

WHERE:

```text
Filters rows before grouping.
```

---

# 5. COMPARISON OPERATORS

Used inside WHERE conditions.

| Operator | Meaning            |
| -------- | ------------------ |
| =        | Equal              |
| !=       | Not Equal          |
| <>       | Not Equal          |
| >        | Greater Than       |
| <        | Less Than          |
| >=       | Greater Than Equal |
| <=       | Less Than Equal    |

---

## Examples

### Equal

```sql
SELECT *
FROM employees
WHERE department = 'IT';
```

---

### Not Equal

```sql
SELECT *
FROM employees
WHERE department != 'IT';
```

---

### Greater Than

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

---

### Less Than

```sql
SELECT *
FROM employees
WHERE salary < 50000;
```

---

## Interview Note

```text
!= and <> both mean Not Equal.
```

---

# 6. LOGICAL OPERATORS

Used to combine conditions.

---

## AND

All conditions must be true.

```sql
SELECT *
FROM employees
WHERE department='IT'
AND salary > 50000;
```

---

## OR

At least one condition must be true.

```sql
SELECT *
FROM employees
WHERE department='IT'
OR department='HR';
```

---

## NOT

Negates a condition.

```sql
SELECT *
FROM employees
WHERE NOT department='HR';
```

---

# Operator Precedence

SQL evaluates:

```text
1. ()
2. NOT
3. AND
4. OR
```

---

Example:

```sql
WHERE age > 18
AND city='Delhi'
OR city='Mumbai'
```

Can give unexpected results.

Prefer:

```sql
WHERE age > 18
AND (city='Delhi'
     OR city='Mumbai')
```

---

## Interview Note

Parentheses improve readability and prevent logical errors.

---

# 7. IN

## Meaning

Checks whether a value exists in a list.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE department IN ('IT','HR','Finance');
```

---

Equivalent To

```sql
WHERE department='IT'
OR department='HR'
OR department='Finance'
```

---

## Why Use IN?

Cleaner and easier to maintain.

---

## Analytics Use Case

Filter multiple departments.

```sql
SELECT *
FROM employees
WHERE department IN ('IT','Finance');
```

---

## Interview Note

Use IN when multiple OR conditions exist.

---

# 8. BETWEEN

## Meaning

Checks whether a value lies within a range.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE salary BETWEEN 50000 AND 100000;
```

---

Equivalent To

```sql
salary >= 50000
AND salary <= 100000
```

---

## Important Rule

BETWEEN is inclusive.

Meaning:

```text
50000 included

100000 included
```

---

## Analytics Use Cases

* Salary ranges
* Date ranges
* Product price ranges

---

## Interview Question

Q. Is BETWEEN inclusive?

Answer:

Yes.

---

# 9. LIKE

## Meaning

The LIKE operator is used for pattern matching in SQL.

Unlike =, which requires an exact match, LIKE allows searching for partial matches.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE employee_name LIKE 'A%';
```

---

## Example 1: Names Starting with A

```sql
SELECT *
FROM employees
WHERE employee_name LIKE 'A%';
```

### Output

```text
Aman
Anjali
Aakash
Aryan
```

---

## Example 2: Names Ending with a

```sql
SELECT *
FROM employees
WHERE employee_name LIKE '%a';
```

### Output

```text
Riya
Ankita
Priya
```

---

## Example 3: Names Containing "an"

```sql
SELECT *
FROM employees
WHERE employee_name LIKE '%an%';
```

### Output

```text
Anjali
Karan
Anand
```

---

## Analytics Use Cases

* Customer search functionality
* Product search
* Filtering cities
* Dashboard filters

---

## Interview Notes

```text
=       → Exact Match

LIKE    → Pattern Match
```

---

# 10. WILDCARDS

Wildcards are special characters used with LIKE.

---

# % Wildcard

## Meaning

Represents:

```text
Zero or More Characters
```

---

## Example

```sql
SELECT *
FROM employees
WHERE employee_name LIKE 'A%';
```

Matches:

```text
A
Aman
Anjali
Aakash
```

---

## Example

```sql
SELECT *
FROM employees
WHERE employee_name LIKE '%an%';
```

Matches:

```text
Anjali
Karan
Anand
```

---

# _ Wildcard

## Meaning

Represents:

```text
Exactly One Character
```

---

## Example

```sql
SELECT *
FROM employees
WHERE employee_name LIKE '_a%';
```

Matches:

```text
Ravi
Karan
```

---

## Difference

```text
% → Any Number of Characters

_ → Exactly One Character
```

---

## Interview Question

Q. Difference between % and _ ?

Answer:

```text
% → Multiple Characters

_ → Single Character
```

---

# 11. ESCAPE CHARACTER

## Meaning

Used when searching for actual wildcard characters.

Suppose table data:

```text
50%
60%
70%
```

You want to search for actual %.

---

## Syntax

```sql
SELECT *
FROM discounts
WHERE discount LIKE '%!%%'
ESCAPE '!';
```

---

## Explanation

```text
! tells SQL that the next character is literal.

% is treated as actual text,
not wildcard.
```

---

## Interview Notes

Rarely used in projects.

Occasionally appears in SQL interviews.

---

# 12. IS NULL

## Meaning

Checks whether a column contains NULL values.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE manager_id IS NULL;
```

---

## Example

| Employee | Manager_ID |
| -------- | ---------- |
| Ravi     | 1          |
| Aman     | NULL       |

Query:

```sql
SELECT *
FROM employees
WHERE manager_id IS NULL;
```

Output:

```text
Aman
```

---

## Why Not Use = NULL ?

Wrong:

```sql
WHERE manager_id = NULL
```

Correct:

```sql
WHERE manager_id IS NULL
```

---

## Interview Question

Q. Why doesn't = NULL work?

Answer:

```text
NULL means Unknown.

Unknown cannot be compared
using = operator.
```

---

# IS NOT NULL

## Meaning

Checks whether values exist.

---

## Syntax

```sql
SELECT *
FROM employees
WHERE manager_id IS NOT NULL;
```

---

## Output

Returns all rows where manager exists.

---

# 13. DISTINCT

## Meaning

Removes duplicate values.

---

## Example

Table:

| Department |
| ---------- |
| IT         |
| IT         |
| HR         |
| Finance    |
| HR         |

---

Query:

```sql
SELECT DISTINCT department
FROM employees;
```

Output:

```text
IT
HR
Finance
```

---

## Multiple Columns

```sql
SELECT DISTINCT department,
                city
FROM employees;
```

---

## Important

DISTINCT applies to the entire combination.

Not individual columns.

---

## Analytics Use Cases

* Unique customers
* Unique cities
* Unique products

---

## Interview Notes

DISTINCT may increase query cost on large datasets.

---

# 14. ORDER BY

## Meaning

Sorts rows.

---

## Ascending Order

```sql
SELECT *
FROM employees
ORDER BY salary ASC;
```

---

## Descending Order

```sql
SELECT *
FROM employees
ORDER BY salary DESC;
```

---

## Default Order

```text
ASC
```

---

## Multiple Columns

```sql
SELECT *
FROM employees
ORDER BY department ASC,
         salary DESC;
```

---

## Analytics Use Cases

* Top customers
* Highest sales
* Lowest expenses

---

## Interview Question

Q. Default ORDER BY?

Answer:

```text
ASC
```

---

# 15. LIMIT

## Meaning

Restricts number of rows returned.

---

## Syntax

```sql
SELECT *
FROM employees
LIMIT 5;
```

---

## Output

Returns first 5 rows.

---

## Analytics Use Cases

Top N records.

```sql
SELECT *
FROM employees
ORDER BY salary DESC
LIMIT 5;
```

Top 5 salaries.

---

## Interview Note

LIMIT executes near the end of query execution.

---

# 16. OFFSET

## Meaning

Skips rows before returning results.

---

## Syntax

```sql
SELECT *
FROM employees
LIMIT 5 OFFSET 10;
```

---

## Explanation

```text
Skip first 10 rows

Return next 5 rows
```

---

## Pagination Example

### Page 1

```sql
LIMIT 10 OFFSET 0
```

### Page 2

```sql
LIMIT 10 OFFSET 10
```

### Page 3

```sql
LIMIT 10 OFFSET 20
```

---

## Analytics Use Cases

* Dashboard pagination
* API pagination
* Reports

---

# LIMIT vs OFFSET

| LIMIT          | OFFSET       |
| -------------- | ------------ |
| Rows to Return | Rows to Skip |

---

# 17. CASE WHEN

## Meaning

SQL's IF-ELSE statement.

Used to create categories.

---

## Syntax

```sql
CASE
    WHEN condition THEN value
    WHEN condition THEN value
    ELSE value
END
```

---

## Example

```sql
SELECT employee_name,
       salary,
       CASE
           WHEN salary > 80000 THEN 'High'
           WHEN salary > 50000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_category
FROM employees;
```

---

## Output

| Salary | Category |
| ------ | -------- |
| 90000  | High     |
| 60000  | Medium   |
| 30000  | Low      |

---

## Analytics Use Cases

### Customer Segmentation

```sql
CASE
    WHEN sales > 100000 THEN 'Premium'
    ELSE 'Regular'
END
```

---

### KPI Categories

```sql
CASE
    WHEN score > 90 THEN 'Excellent'
    WHEN score > 70 THEN 'Good'
    ELSE 'Average'
END
```

---

## Interview Notes

CASE is heavily used in:

* Power BI
* Tableau
* Reporting
* Dashboards
* Data Warehousing

---

## Revision Summary (Sections 9–17)

```text
LIKE            → Pattern Matching

%               → Multiple Characters

_               → Single Character

ESCAPE          → Literal Wildcards

IS NULL         → Missing Values

DISTINCT        → Remove Duplicates

ORDER BY        → Sorting

LIMIT           → Restrict Rows

OFFSET          → Skip Rows

CASE WHEN       → Conditional Logic
```

# 18. COUNT()

## Meaning

Counts records.

---

## Syntax

```sql
SELECT COUNT(*)
FROM employees;
```

---

## Example

```sql
SELECT COUNT(*)
FROM employees;
```

Output:

```text
3
```

---

## COUNT(column)

```sql
SELECT COUNT(manager_id)
FROM employees;
```

Counts only non-null values.

---

## Difference

### COUNT(*)

Counts all rows.

### COUNT(column)

Ignores NULL values.

---

## Interview Question

```text
COUNT(*) vs COUNT(column)
```

Answer:

COUNT(column) ignores NULLs.

---

# 19. SUM()

## Meaning

Adds numeric values.

---

## Syntax

```sql
SELECT SUM(salary)
FROM employees;
```

---

## Example

| salary |
| ------ |
| 30000  |
| 40000  |
| 50000  |

Result:

```text
120000
```

---

## Analytics Use Cases

* Revenue
* Profit
* Sales
* Expenses

---

# 20. AVG()

## Meaning

Calculates average value.

---

## Syntax

```sql
SELECT AVG(salary)
FROM employees;
```

---

## Example

```text
30000
40000
50000
```

Average:

```text
40000
```

---

## Important

AVG ignores NULL values.

---

## Analytics Use Cases

* Average Salary
* Average Sales
* Average Rating

---

# 21. MIN()

## Meaning

Returns smallest value.

---

## Syntax

```sql
SELECT MIN(salary)
FROM employees;
```

---

## Example

```text
30000
40000
50000
```

Result:

```text
30000
```

---

## Analytics Use Cases

* Lowest Salary
* Lowest Price
* Earliest Date

---

# 22. MAX()

## Meaning

Returns largest value.

---

## Syntax

```sql
SELECT MAX(salary)
FROM employees;
```

---

## Example

```text
30000
40000
50000
```

Result:

```text
50000
```

---

## Analytics Use Cases

* Highest Salary
* Highest Revenue
* Latest Date

---

# 23. GROUP BY

## Meaning

Groups rows with the same values.

Without GROUP BY:

```sql
SELECT COUNT(*)
FROM employees;
```

Output:

```text
50
```

Only one result.

---

## Example

```sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department;
```

---

## Output

| department | count |
| ---------- | ----- |
| IT         | 20    |
| HR         | 10    |
| Finance    | 20    |

---

## Why Use GROUP BY?

To answer business questions like:

* Department-wise salary
* City-wise revenue
* Product-wise sales

---

## GROUP BY Rule ⭐

Every selected column must either:

```text
1. Be aggregated

OR

2. Appear in GROUP BY
```

---

### Wrong

```sql
SELECT department,
       salary
FROM employees
GROUP BY department;
```

❌ Invalid

---

### Correct

```sql
SELECT department,
       AVG(salary)
FROM employees
GROUP BY department;
```

---

## Multiple Column GROUP BY

```sql
SELECT department,
       city,
       COUNT(*)
FROM employees
GROUP BY department,
         city;
```

---

## Analytics Use Cases

### Department-wise Average Salary

```sql
SELECT department,
       AVG(salary)
FROM employees
GROUP BY department;
```

---

### City-wise Customer Count

```sql
SELECT city,
       COUNT(*)
FROM customers
GROUP BY city;
```

---

# 24. HAVING

## Meaning

Filters grouped data.

WHERE filters rows.

HAVING filters groups.

---

## Syntax

```sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

---

## Example

Suppose:

| department | employees |
| ---------- | --------- |
| IT         | 10        |
| HR         | 2         |
| Finance    | 8         |

Query:

```sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

Output:

| department | count |
| ---------- | ----- |
| IT         | 10    |
| Finance    | 8     |

---

## Why Not WHERE?

Wrong:

```sql
SELECT department,
       COUNT(*)
FROM employees
WHERE COUNT(*) > 5
GROUP BY department;
```

❌ Invalid

Aggregate functions cannot be used in WHERE.

---

## WHERE vs HAVING

| WHERE           | HAVING             |
| --------------- | ------------------ |
| Filters Rows    | Filters Groups     |
| Before GROUP BY | After GROUP BY     |
| No Aggregates   | Aggregates Allowed |

---

## Analytics Use Cases

### Departments Having More Than 5 Employees

```sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

---

### Cities With Revenue Above 1 Million

```sql
SELECT city,
       SUM(revenue)
FROM sales
GROUP BY city
HAVING SUM(revenue) > 1000000;
```

---

## Most Important SQL Interview Question ⭐

Q. Difference Between WHERE and HAVING?

Answer:

```text
WHERE filters rows.

HAVING filters groups.
```
# 25. COMMON SQL PITFALLS ⚠️

These are mistakes that beginners make frequently and interviewers love asking about.

---

# Pitfall 1: Using = NULL

❌ Wrong

```sql id="o71q14"
SELECT *
FROM employees
WHERE manager_id = NULL;
```

✅ Correct

```sql id="5ygn93"
SELECT *
FROM employees
WHERE manager_id IS NULL;
```

---

## Why?

NULL means:

```text id="vqqdzm"
Unknown Value
```

Unknown values cannot be compared using:

```text id="g6xekd"
=
!=
<>
```

---

# Pitfall 2: Using Alias in WHERE

❌ Wrong

```sql id="4bjykr"
SELECT salary AS sal
FROM employees
WHERE sal > 50000;
```

---

✅ Correct

```sql id="w0h16e"
SELECT salary AS sal
FROM employees
WHERE salary > 50000;
```

---

## Why?

Because:

```text id="m8g6q6"
WHERE executes before SELECT.
```

---

# Pitfall 3: Forgetting BETWEEN is Inclusive

Query:

```sql id="kh5fg8"
WHERE salary BETWEEN 50000 AND 100000
```

Actually means:

```sql id="ffmyr5"
salary >= 50000
AND salary <= 100000
```

Both limits are included.

---

# Pitfall 4: Confusing WHERE and HAVING

❌ Wrong

```sql id="4xvl9o"
SELECT department,
       COUNT(*)
FROM employees
WHERE COUNT(*) > 5
GROUP BY department;
```

---

✅ Correct

```sql id="ajxsgj"
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

---

# Pitfall 5: Missing GROUP BY Columns

❌ Wrong

```sql id="4ht8hj"
SELECT department,
       salary
FROM employees
GROUP BY department;
```

---

✅ Correct

```sql id="7qdnfh"
SELECT department,
       AVG(salary)
FROM employees
GROUP BY department;
```

---

# Pitfall 6: Using SELECT *

❌

```sql id="1uyjbd"
SELECT *
FROM employees;
```

---

✅

```sql id="wn40ph"
SELECT employee_name,
       salary
FROM employees;
```

---

## Why?

* Better Performance
* Better Readability
* Less Data Transfer

---

# Pitfall 7: Missing WHERE in UPDATE

❌

```sql id="5w5s0e"
UPDATE employees
SET salary = 50000;
```

---

Result:

```text id="qbf0i3"
Every employee salary becomes 50000.
```

---

✅

```sql id="0w0vva"
UPDATE employees
SET salary = 50000
WHERE employee_id = 5;
```

---

# Pitfall 8: Missing WHERE in DELETE

❌

```sql id="8c1m8t"
DELETE FROM employees;
```

Deletes all rows.

---

✅

```sql id="7b53oq"
DELETE FROM employees
WHERE employee_id = 5;
```

---

# Pitfall 10: COUNT(*) vs COUNT(column)

### COUNT(*)

Counts all rows.

### COUNT(column)

Ignores NULL values.

Example:

| id | manager_id |
| -- | ---------- |
| 1  | NULL       |
| 2  | 1          |
| 3  | 1          |

```sql id="57mw0s"
SELECT COUNT(*)
FROM employees;
```

Result:

```text id="yg29n4"
3
```

---

```sql id="m1wd3l"
SELECT COUNT(manager_id)
FROM employees;
```

Result:

```text id="mww87n"
2
```

---

# 30. MOST IMPORTANT SQL INTERVIEW QUESTIONS ⭐

---

## Q1. Difference Between WHERE and HAVING?

| WHERE           | HAVING             |
| --------------- | ------------------ |
| Filters Rows    | Filters Groups     |
| Before GROUP BY | After GROUP BY     |
| No Aggregates   | Aggregates Allowed |

---

## Q2. Difference Between INNER JOIN and LEFT JOIN?

### INNER JOIN

Returns matching rows only.

### LEFT JOIN

Returns all rows from left table plus matching rows.

---

## Q3. Difference Between DELETE, TRUNCATE, and DROP?

| DELETE        | TRUNCATE         | DROP          |
| ------------- | ---------------- | ------------- |
| Deletes Rows  | Deletes All Rows | Deletes Table |
| WHERE Allowed | No WHERE         | No WHERE      |
| Table Exists  | Table Exists     | Table Removed |
| Slower        | Faster           | Fastest       |

---

## Q4. Difference Between COUNT(*) and COUNT(column)?

### COUNT(*)

Counts every row.

### COUNT(column)

Ignores NULL values.

---

## Q5. Why Can't Alias Be Used in WHERE?

Because:

```text id="n56dfv"
WHERE executes before SELECT.
```

---

## Q6. What Is the Difference Between % and _ ?

### %

Matches multiple characters.

### _

Matches exactly one character.

---

## Q7. Is BETWEEN Inclusive?

Yes.

```sql id="z8r7ri"
BETWEEN 10 AND 20
```

means:

```sql id="jlwmg7"
>= 10
<= 20
```

---

## Q8. Why Use GROUP BY?

Used to summarize data.

Examples:

```text id="r0yx4m"
Department-wise Salary

City-wise Revenue

Product-wise Sales
```

---

## Q9. What Is a SELF JOIN?

A table joined with itself using aliases.

Example:

```text id="z3hdbz"
Employee → Manager
```

---

## Q10. SQL Query Execution Order?

```text id="is4htg"
FROM
JOIN
WHERE
GROUP BY
HAVING
SELECT
DISTINCT
ORDER BY
LIMIT
```

One of the most frequently asked SQL interview questions.

---

# 31. QUICK REVISION TABLE 🚀

| Topic      | Purpose             |
| ---------- | ------------------- |
| SELECT     | Retrieve Data       |
| Alias      | Temporary Name      |
| WHERE      | Filter Rows         |
| IN         | Match List          |
| BETWEEN    | Match Range         |
| LIKE       | Pattern Matching    |
| %          | Multiple Characters |
| _          | Single Character    |
| ESCAPE     | Literal Wildcards   |
| IS NULL    | Missing Values      |
| DISTINCT   | Remove Duplicates   |
| ORDER BY   | Sort Data           |
| LIMIT      | Restrict Rows       |
| OFFSET     | Skip Rows           |
| CASE WHEN  | Conditional Logic   |
| INNER JOIN | Matching Records    |
| LEFT JOIN  | All Left Records    |
| RIGHT JOIN | All Right Records   |
| SELF JOIN  | Self Relationship   |
| COUNT      | Count Rows          |
| SUM        | Total               |
| AVG        | Average             |
| MIN        | Minimum             |
| MAX        | Maximum             |
| GROUP BY   | Create Groups       |
| HAVING     | Filter Groups       |

---

# FINAL REVISION CHECKLIST ✅

## Fundamentals

* [ ] Query Execution Order
* [ ] SELECT
* [ ] Alias
* [ ] WHERE
* [ ] Comparison Operators
* [ ] Logical Operators
* [ ] IN
* [ ] BETWEEN
* [ ] LIKE
* [ ] Wildcards
* [ ] ESCAPE Character
* [ ] IS NULL
* [ ] DISTINCT
* [ ] ORDER BY
* [ ] LIMIT
* [ ] OFFSET
* [ ] CASE WHEN

---

## Joins

* [ ] INNER JOIN
* [ ] LEFT JOIN
* [ ] RIGHT JOIN
* [ ] SELF JOIN

---

## Aggregations

* [ ] COUNT()
* [ ] SUM()
* [ ] AVG()
* [ ] MIN()
* [ ] MAX()

---

## Grouping

* [ ] GROUP BY
* [ ] HAVING

---

## Interview Concepts

* [ ] WHERE vs HAVING
* [ ] INNER JOIN vs LEFT JOIN
* [ ] COUNT(*) vs COUNT(column)
* [ ] DELETE vs TRUNCATE vs DROP
* [ ] Query Execution Order
* [ ] Alias in WHERE
* [ ] BETWEEN Inclusiveness