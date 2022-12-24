<img src="assets/postgresql-logo.svg" width="250" height="250">

# PostgreSQL Summary (Advanced) <!-- omit from toc -->

## Table of contents <!-- omit from toc -->

- [1. Subqueries and CTEs](#1-subqueries-and-ctes)
  - [1.1. Subqueries](#11-subqueries)
  - [1.2. Common Table Expressions](#12-common-table-expressions)
  - [1.3. Subqueries for Comparisons](#13-subqueries-for-comparisons)
  - [1.4. Recursive CTEs](#14-recursive-ctes)
- [2. Window Functions](#2-window-functions)
- [3. Advanced JOIN Operations](#3-advanced-join-operations)
  - [3.1. Cross Join](#31-cross-join)
  - [3.2. Full Join](#32-full-join)
  - [3.3. USING and Natural Joins](#33-using-and-natural-joins)
- [4. Set Operations](#4-set-operations)
  - [4.1. Union](#41-union)
  - [4.2. Intersect](#42-intersect)
  - [4.3. Except](#43-except)


<br>
<br>

****************
## 1. Subqueries and CTEs

Different flavours of subqueries:
- Basic subquery - FROM clause
- Common table expressions/WITH clauses
- Comparisons with main data set


### 1.1. Subqueries
- Subqueries in `FROM` clauses
It's basically a `SELECT` statement inside a `SELECT` statement.
```sql
SELECT col_1, col_2, …
FROM (
	SELECT col_1, col_2, …
	FROM table_name
) table_alias
```

- Subqueries in `JOIN` clauses
```sql
SELECT t1.col_1, t2.col_2, …
FROM table_1 t1
INNER JOIN (
SELECT col_1 FROM table_2
) t2 on t1.col_1 = t2.col_2
```

You can nest as many queries as you want.
Subqueries enable you to write more complex queries.
However, they can also be hard to read.
```sql
SELECT se.*
FROM (
    SELECT *
    FROM surgical_encounters
    WHERE surgical_admission_date
            BETWEEN '2016-11-01' AND '2016-11-30'
    ) se
INNER JOIN (
    SELECT master_patient_id
    FROM patients
    WHERE date_of_birth >= '1990-01-01'
    ) p ON  SE.master_patient_id = P.master_patient_id;
```

### 1.2. Common Table Expressions
Common table expressions (CTEs) provide a way to break down complex queries and make them easier to understand. CTEs create tables that only exist for a single query and can be re-used in a single query. They are good for performing complex, multi-step calculations.

CTEs are identified by a `WITH` clause and they can be used with `CREATE`, `SELECT`, `UPDATE`, and `DELETE` operations:
```sql
WITH table_name AS (SELECT column_name)
```

```sql
WITH young_patients AS (
    SELECT *
    FROM patients
    WHERE date_of_birth >= '2000-01-01'
)
SELECT * 
FROM young_patients
WHERE name ILIKE 'm%';
```

Now we try to get The number of surgeries By counties where we have more than 1500 patients:
```sql
WITH top_counties AS (
    SELECT
        county,
        COUNT(*) AS num_patients
    FROM patients
    GROUP BY county
    HAVING COUNT(0) > 1500
    ),
    county_patients AS (
        SELECT
            p.master_patient_id,
            p.county
        FROM patients p
        INNER JOIN top_counties t ON
            p.county = t.county
    )
SELECT
    p.county,
    COUNT(s.surgery_id) AS num_surgeries
FROM surgical_encounters s
INNER JOIN county_patients p ON
    s.master_patient_id = p.master_patient_id
GROUP BY p.county
```


### 1.3. Subqueries for Comparisons
Subqueries can also beused in `WHERE` and `HAVING` caluses which are useful for writing comparisons against values that are dynamic or unknown.
They can be used with most comparison operators of interest: `>`, `>=`, `<`, `<=`, `!=`, `<>`, `LIKE`, etc.

```sql
WITH total_cost AS (
    SELECT
        surgery_id,
        SUM(resource_cost) AS total_surgery_cost
    FROM surgical_costs
    GROUP BY surgery_id
    )

SELECT *
FROM total_cost
WHERE total_surgery_cost > (
    SELECT AVG(total_surgery_cost)
    FROM total_cost
);
```
```sql
SELECT *
FROM vitals
WHERE
    bp_diastolic > (SELECT MIN(bp_diastolic) FROM vitals)
    AND bp_systolic < (SELECT MAX(bp_systolic) FROM vitals)
;
```

Subqueries can be useful for comparing sets of values using `IN` and `NOT IN` where set is not known beforehand.
```sql
SELECT *
FROM patients
WHERE master_patient_id IN (
    SELECT DISTINCT master_patient_id FROM surgical_encounters
)
ORDER BY master_patient_id;
```

**Note:** Subqueries with `IN` and `NOT IN` can often be written as joins, depending on performance.
```sql
SELECT DISTINCT p.master_patient_id
FROM patients p
INNER JOIN surgical_encounters s
    ON p.master_patient_id = s.master_patient_id
ORDER BY p.master_patient_id;
```

`ANY` or `ALL` must be preceded by an operator (`>`, `>=`, `<`, `<=`, `=`, `!=`, `<>`, `LIKE`, …) and can be used in `WHERE` or `HAVING` clauses.

| Clause   | Equivalent to |
| -------- | ------------- |
| `SOME`   | `ANY`         |
| `IN`     | `ANY`         |
| `NOT IN` | `<> ALL`      |

**Note:** If there's no successes/True values for comparison and at least one null evaluation for operator, the result will be null.
```sql
SELECT *
FROM surgical_encounters
WHERE total_profit > ALL (
    SELECT AVG(total_cost)
    FROM surgical_encounters
    GROUP BY diagnosis_description
);
```
```sql
SELECT
    diagnosis_description,
    AVG(surgical_discharge_date - surgical_admission_date)
        AS length_of_stay
FROM surgical_encounters
GROUP BY diagnosis_description
HAVING AVG(surgical_discharge_date - surgical_admission_date) <=
    ALL(
        SELECT
            AVG(EXTRACT(DAY FROM patient_discharge_datetime - patient_admission_datetime))
        FROM encounters
        GROUP BY department_id
    );
```

`EXISTS` is used to see whether a subquery returns any results. It is used with `WHERE` clause. Subqueries with `EXISTS` can sometimes be inefficient and have poor performance.

**Note:** When subquery returns null, result of `EXISTS` evaluates to True.
```sql
SELECT p.*
FROM patients p
WHERE NOT EXISTS(
    SELECT 1
    FROM surgical_encounters s
    WHERE s.master_patient_id = p.master_patient_id
);
```

### 1.4. Recursive CTEs
Recursion involves a function or process referring to itself.
Recursive CTEs in Postgres provide a powerful tool for constructing or analyzing network- or tree-like relationships.
Start with a non-recursive base term in `WITH` clause and add `RECURSIVE` keyword
so that postgres understands that we're going to construct a recursive query.

```sql 
WITH RECURSIVE orders AS (

    SELECT
        ORDER_PROCEDURE_ID,
        ORDER_PARENT_ORDER_ID,
        0 AS LEVEL  -- This is going to be base level
    FROM orders_procedures
    WHERE order_parent_order_id IS null
    UNION ALL
    SELECT
        op.orders_procedure_id,
        op.order_parent_order_id,
        o.level +1 AS Level
    FROM orders_procedures op
    INNER JOIN orders o ON op.order_parent_order_id = o.order_parent_order_id
)
SELECT *
FROM orders;
```


<br>
<br>

****************
## 2. Window Functions
Window functions have two categories/uses:
- Aggregating data by group within data
- Performing dynamic calculations on data within groups

In order to use window we must use `OVER` clause. The contents of the `OVER` clause determine how Postgres divides and sorts data for window function calculations.
`OVER()` with no additional arguments applies window function to entire selection.

- `PARTITION BY` clause determines how data is grouped.

**Note:** Window functions are executed after all other filters, aggeregations, etc and they're **not** allowed in `WHERE`, `HAVING`, or `GROUP BY` clauses.

```sql
WITH surgical_los AS (
    SELECT
        surgery_id,
        (surgical_discharge_date - surgical_admission_date) AS los,
        AVG(surgical_discharge_date - surgical_admission_date)
            OVER(PARTITION BY primary_icd
                ORDER BY total_account_balance DESC) AS avg_los
    FROM surgical_encounters
    )
SELECT
    *,
    ROUND(los - avg_los,2) AS over_under
FROM surgical_los;    
```

`WINDOW` clause enables re-use the same window for multiple calculations.
Notice that it must be defined **after** the `FROM` clause.

```sql
SELECT
    s.surgery_id,
    p.full_name,
    s.total_profit,
    AVG(total_profit) OVER w AS avg_total_profit,
    s.total_cost,
    SUM(total_cost) OVER w AS total_surgeon_cost
FROM surgical_encounters s
LEFT OUTER JOIN physicians p
    ON s.surgeon_id = p.id
WINDOW w AS (PARTITION BY s.surgeon_id)
```

| Function       | Description                                                                                                                                                                                                                                                                                                                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AVG()`        | Average over the window function                                                                                                                                                                                                                                                                                                   |
| `SUM()`        | Summation over the window function                                                                                                                                                                                                                                                                                                 |
| `STRING_AGG()` | Concatenation of the strings over the window function                                                                                                                                                                                                                                                                              |
| `RANK()`       | Returns the rank of the current row, with gaps; that is, the row_number of the first row in its peer group.                                                                                                                                                                                                                        |
| `DENSE_RANK()` | Returns the rank of the current row, <u>without gaps</u>; this function effectively counts peer groups.                                                                                                                                                                                                                            |
| `ROW_NUMBER()` | Returns the number of the current row within its partition, counting from 1.                                                                                                                                                                                                                                                       |
| `LAG()`        | Returns value evaluated at the row that is offset rows before the current row within the partition; if there is no such row, instead returns default (which must be of a type compatible with value). Both offset and default are evaluated with respect to the current row. If omitted, offset defaults to 1 and default to NULL. |
| `LEAD()`       | Returns value evaluated at the row that is offset rows after the current row within the partition; if there is no such row, instead returns default (which must be of a type compatible with value). Both offset and default are evaluated with respect to the current row. If omitted, offset defaults to 1 and default to NULL.  |

See the full list: [https://www.postgresql.org/docs/current/functions-window.html](https://www.postgresql.org/docs/current/functions-window.html) 


<br>
<br>

****************
## 3. Advanced JOIN Operations 

### 3.1. Cross Join
A cross join generates all combinations of the rows of table 1 and the rows of table 2. It's useful for generating all combinations of instances.

Imagine we have these two tables:
```
Table 1 - t1
col_1   col_2
-----   -----
a       b
a       c

Table 2 - t2
col_1   col_2
-----   -----
1       2
1       3
```

You can cross join the two tables using either of the two syntaxes below:
```sql
SELECT
    t1.col_1,
    t2.col_1
FROM table_1 t1
CROSS JOIN table_2 t2
```
Or
```sql
SELECT
    t1.col_1,
    t2.col_1
FROM table_1 t1, table_2 t2
```

The result of `CROSS JOIN` would be the following:

```
t1.col_1    t1.col_2    t2.col_1    t2.col_2
--------    --------    --------    --------
a           b           1           2
a           c           1           3
a           b           1           2
a           c           1           3
```

### 3.2. Full Join
A `FULL JOIN` is a combination of a `LEFT JOIN` and `RIGHT JOIN` and it returns records that are in:
1. Both tables
2. Table 1, but not Table 2
3. Table 2, but not Table 1

```sql
SELECT
    t1.column_1,
	t2.column_1
FROM table_1 t1
FULL JOIN table_2 t2
	ON t1.column_1 = t2.column_1
WHERE t2.column_1 IS NULL
```

**Examples of use cases:**

- Find orders without visits and/or visits without orders
- Find employees without departments and/or departments without employees
- Useful for finding data quality issues. For example: `NOT NULL` condition not being enforced



### 3.3. USING and Natural Joins
Databases will often have columns with the same name in multiple tables.
`NATURAL JOIN` and `USING` let us take advantage of this for our join conditions.

```sql
SELECT
	t1.column_1,
	t2.column_1
FROM table_1 t1
INNER JOIN table_2 t2 
USING (column_1, column_2)  -- This specifies which columns are the same in both tables
```

```sql
SELECT
	t1.column_1,
	t2.column_1
FROM table_1 t1
NATURAL JOIN table_2 t2
```
`NATURAL JOIN` includes all common column names between tables and acts like an implicit `USING` clause. (`INNER JOIN` by default)

**Note:** If the two tables have no common columns, `NATURAL JOIN` = `CROSS JOIN`. Therefore, it's usually better to use `USING`.


<br>
<br>

****************
## 4. Set Operations

### 4.1. Union
`UNION` adds the result of one query to the result of another query.
- Both queries must have the same number of columns
- Columns must have the same or similar data types
- Duplicate values <u>will be removed</u> unless `UNION ALL` is used

```sql
SELECT
	column_1
FROM table_1
UNION
SELECT
	column_1
FROM table_2
```

### 4.2. Intersect
`INTERSECT` returns rows that are in the result of both query 1 and query 2.
- Queries must have the same number of columns and convertible data types
- Duplicate values will be removed unless `INTERSECT ALL` is used

```sql
SELECT
	column_1
FROM table_1
INTERSECT
SELECT
	column_1
FROM table_2
```

### 4.3. Except
`EXCEPT` returns rows that are in the result of query 1 but not query 2.
- Queries must have the same number of columns and convertible data types
- Duplicate values will be removed unless `EXCEPT ALL` is used

```sql
SELECT
	column_1
FROM table_1
EXCEPT
SELECT
	column_1
FROM table_2
```
