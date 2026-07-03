# SQL_PATTERNS.md

---

# Pattern 1: String Length Validation

## Problem

Invalid Tweets

## Query

```sql
SELECT tweet_id
FROM Tweets
WHERE LENGTH(content) > 15;
```

## Learning

* LENGTH()
* String validation
* Filtering using WHERE

## Generic Pattern

```sql
SELECT *
FROM table_name
WHERE LENGTH(column_name) > n;
```

## Note

Character count / text length questions → think LENGTH().

---

# Pattern 2: Conditional Aggregation

## Problem

Monthly Transactions I

## Query

```sql
SELECT
    DATE_FORMAT(trans_date,'%Y-%m') AS month,
    country,
    COUNT(id) AS trans_count,
    SUM(CASE WHEN state='approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state='approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY month, country;
```

## Learning

* DATE_FORMAT()
* CASE WHEN
* Conditional Aggregation
* GROUP BY

## Generic Pattern

```sql
SUM(
    CASE
        WHEN condition THEN value
        ELSE 0
    END
)
```

## Note

Conditional counts/sums → CASE WHEN inside SUM().

---

# Pattern 3: Daily Active Users

## Problem

User Activity for the Past 30 Days I

## Query

```sql
SELECT
    activity_date AS day,
    COUNT(DISTINCT user_id) AS active_users
FROM Activity
WHERE activity_date > '2019-06-27'
AND activity_date <= '2019-07-27'
GROUP BY activity_date;
```

## Learning

* COUNT(DISTINCT)
* Date Filtering
* GROUP BY

## Generic Pattern

```sql
SELECT date_col,
       COUNT(DISTINCT user_id)
FROM table_name
GROUP BY date_col;
```

## Note

Unique users/customers/visitors → COUNT(DISTINCT).

---

# Pattern 4: Generate Missing Combinations

## Problem

Students and Examinations

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
GROUP BY s.student_id,
         s.student_name,
         sub.subject_name
ORDER BY student_id,
         subject_name;
```

## Learning

* CROSS JOIN
* LEFT JOIN
* COUNT(column)

## Generic Pattern

```sql
SELECT *
FROM A
CROSS JOIN B
LEFT JOIN C
ON condition;
```

## Note

Need all possible combinations → CROSS JOIN.

---

# Pattern 5: Bought All Products

## Problem

Customers Who Bought All Products

## Query

```sql
SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) =
(
    SELECT COUNT(*)
    FROM Product
);
```

## Learning

* COUNT(DISTINCT)
* HAVING
* Scalar Subquery

## Generic Pattern

```sql
SELECT entity_id
FROM table_name
GROUP BY entity_id
HAVING COUNT(DISTINCT item_id) =
(
    SELECT COUNT(*)
    FROM master_table
);
```

## Note

"Bought all", "completed all", "visited all" → compare individual count with total count.

---

