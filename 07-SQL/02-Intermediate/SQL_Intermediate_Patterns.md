# SQL Intermediate Patterns

# Pattern 1 – Filter Rows Using an Aggregate Value

## Pattern

```sql
SELECT *
FROM table_name
WHERE column_name >
(
    SELECT AGG(column_name)
    FROM table_name
);
```

## Use Cases

- Employees earning above average salary.
- Students scoring above average marks.
- Products priced above average.

---

# Pattern 2 – Match Group-wise Aggregate

## Pattern

```sql
SELECT *
FROM table_name
WHERE (group_column, value_column) IN
(
    SELECT group_column,
           AGG(value_column)
    FROM table_name
    GROUP BY group_column
);
```

## Use Cases

- Highest salary in each department.
- Lowest price in each category.
- Highest marks in each class.

---

# Pattern 3 – Find Missing Records

## Pattern

```sql
SELECT *
FROM table1
WHERE column_name NOT IN
(
    SELECT column_name
    FROM table2
);
```

## Use Cases

- Departments without employees.
- Customers without orders.
- Students who haven't submitted assignments.

# Pattern 4 – Compare Row with Group Average

## Problem

Find rows whose value is greater than the average value of their own group.

## Pattern

```sql
SELECT *
FROM table_name t1
WHERE value_column >
(
    SELECT AVG(value_column)
    FROM table_name t2
    WHERE t2.group_column = t1.group_column
);
```

## Use Cases

- Employees earning above their department's average salary.
- Students scoring above their class average.
- Products priced above their category average.

---

# Pattern 5 – Find Missing Records using NOT EXISTS

## Pattern

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

## Use Cases

- Departments without employees.
- Customers without orders.
- Products never sold.

# Pattern 6 – Compare Group Totals with Overall Average

## Problem

Find groups whose total value is greater than the average total value of all groups.

## Pattern

```sql
SELECT *
FROM
(
    SELECT group_column,
           SUM(value_column) AS total_value
    FROM table_name
    GROUP BY group_column
) t1
JOIN
(
    SELECT AVG(total_value) AS avg_value
    FROM
    (
        SELECT group_column,
               SUM(value_column) AS total_value
        FROM table_name
        GROUP BY group_column
    ) t2
) avg_result
ON t1.total_value > avg_result.avg_value;
```

### Better Solution

Use a CTE to avoid repeating the same subquery.

```sql
WITH totals AS
(
    SELECT group_column,
           SUM(value_column) AS total_value
    FROM table_name
    GROUP BY group_column
)

SELECT *
FROM totals
JOIN
(
    SELECT AVG(total_value) AS avg_value
    FROM totals
) avg_result
ON totals.total_value > avg_result.avg_value;
```

# Pattern 7: Missing Records

## Problem

Customer Who Visited but Did Not Make Any Transactions

## Query

```sql
SELECT customer_id,
       COUNT(visits.visit_id) AS count_no_trans
FROM Visits
LEFT JOIN Transactions
ON Visits.visit_id = Transactions.visit_id
WHERE Transactions.transaction_id IS NULL
GROUP BY customer_id;
```

## Learning

* LEFT JOIN
* IS NULL
* Missing Records

## Generic Pattern

```sql
SELECT *
FROM A
LEFT JOIN B
ON A.id = B.id
WHERE B.id IS NULL;
```

## Note

Missing / No / Without / Never → LEFT JOIN + IS NULL.

---

# Pattern 8: Compare Consecutive Rows

## Problem

Rising Temperature

## Query

```sql
SELECT w.id
FROM Weather w
JOIN Weather v
ON DATEDIFF(w.recordDate, v.recordDate) = 1
WHERE w.temperature > v.temperature;
```

## Learning

* SELF JOIN
* DATEDIFF()
* Previous Row Comparison

## Generic Pattern

```sql
SELECT *
FROM table_name t1
JOIN table_name t2
ON DATEDIFF(t1.date_col, t2.date_col) = 1;
```

## Note

Previous day/month/record comparison → SELF JOIN (later LAG()).

---

# Quick Pattern Index

| Pattern | Core Concept           |
| ------- | ---------------------- |
| 1       | LENGTH()               |
| 2       | CASE WHEN + SUM()      |
| 3       | COUNT(DISTINCT)        |
| 4       | CROSS JOIN             |
| 5       | HAVING + Subquery      |
| 6       | LEFT JOIN + IS NULL    |
| 7       | SELF JOIN + DATEDIFF() |

```
```
# Pattern 9: Pair Start and End Events

## Problem

Average Time of Process per Machine

## Query

```sql
SELECT
    a.machine_id,
    ROUND(AVG(b.timestamp - a.timestamp), 3) AS processing_time
FROM Activity a
JOIN Activity b
ON a.machine_id = b.machine_id
AND a.process_id = b.process_id
AND a.activity_type = 'start'
AND b.activity_type = 'end'
GROUP BY a.machine_id;
```

## Learning

* SELF JOIN
* Pairing Related Records
* AVG()
* ROUND()

## Generic Pattern

```sql
SELECT
    AVG(end.time - start.time)
FROM table start
JOIN table end
ON start.id = end.id
AND start.type = 'start'
AND end.type = 'end';
```

## Note

When rows represent different states/events (start-end, login-logout, checkin-checkout), use SELF JOIN to pair them.

---
# Pattern 10: Generate Missing Combinations

## Problem

1280. Students and Examinations

## Query

```sql
SELECT
    s.student_id,
    s.student_name,
    sub.subject_name,
    COUNT(e.subject_name) AS attended_exams
FROM Students s
CROSS JOIN Subjects sub
LEFT JOIN Examinations e
ON s.student_id = e.student_id
AND sub.subject_name = e.subject_name
GROUP BY
    s.student_id,
    s.student_name,
    sub.subject_name
ORDER BY
    student_id,
    subject_name;
```

## Learning

* CROSS JOIN
* LEFT JOIN
* COUNT(column)
* Generate all possible combinations

## Generic Pattern

```sql
SELECT *
FROM A
CROSS JOIN B
LEFT JOIN C
ON condition;
```

## Note

Need every possible combination (Student×Subject, Customer×Product, Employee×Training) → CROSS JOIN first.
---

