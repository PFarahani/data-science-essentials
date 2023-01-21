<img src="assets/postgresql-logo.svg" width="250" height="250">

# PostgreSQL Summary (Advanced) <!-- omit from toc -->

## Table of contents <!-- omit from toc -->

- [1. Subqueries and CTEs](#1-subqueries-and-ctes)
  - [1.1. Subqueries](#11-subqueries)
  - [1.2. Common Table Expressions](#12-common-table-expressions)
  - [1.3. Subqueries for Comparisons](#13-subqueries-for-comparisons)
  - [1.4. Recursive CTEs](#14-recursive-ctes)
- [2. Window Functions](#2-window-functions)
  - [2.1. Find Duplicates with RANK](#21-find-duplicates-with-rank)
- [3. Advanced JOIN Operations](#3-advanced-join-operations)
  - [3.1. Cross Join](#31-cross-join)
  - [3.2. Full Join](#32-full-join)
  - [3.3. USING and Natural Joins](#33-using-and-natural-joins)
- [4. Set Operations](#4-set-operations)
  - [4.1. Union](#41-union)
  - [4.2. Intersect](#42-intersect)
  - [4.3. Except](#43-except)
- [5. Grouping Sets](#5-grouping-sets)
  - [5.1. Grouping Sets](#51-grouping-sets)
  - [5.2. Cube](#52-cube)
  - [5.3. Rollup](#53-rollup)
- [6. Schema Structures and Table Relationships](#6-schema-structures-and-table-relationships)
  - [6.1. Information Schema](#61-information-schema)
  - [6.2. Comment](#62-comment)
  - [6.3. Adding and Dropping Constraints](#63-adding-and-dropping-constraints)
  - [6.4. Adding and Dropping Foreign Keys](#64-adding-and-dropping-foreign-keys)
- [7. Transactions](#7-transactions)
  - [7.1. Update and Set](#71-update-and-set)
  - [7.2. Beginning and Ending Transactions](#72-beginning-and-ending-transactions)
  - [7.3. Savepoint](#73-savepoint)
  - [7.4. Database Locks](#74-database-locks)
- [8. Table Inheritance and Partitioning](#8-table-inheritance-and-partitioning)
  - [8.1. Range Partitioning](#81-range-partitioning)
  - [8.2. List Partitioning](#82-list-partitioning)
  - [8.3. Hash Partitioning](#83-hash-partitioning)
  - [8.4. Table Inheritance](#84-table-inheritance)
- [9. Views](#9-views)
  - [9.1. Creating Views](#91-creating-views)
  - [9.2. Modifying and Deleting Views](#92-modifying-and-deleting-views)
  - [9.3. Updatable Views](#93-updatable-views)
  - [9.4. Materialized Views](#94-materialized-views)
  - [9.5. Recursive Views](#95-recursive-views)
- [10. User-Defined Functions](#10-user-defined-functions)
  - [10.1. Creating Functions](#101-creating-functions)
  - [10.2. Modifying Functions](#102-modifying-functions)
- [11. Stored Procedures](#11-stored-procedures)
  - [11.1. Creating and Calling Stored Procedures](#111-creating-and-calling-stored-procedures)
  - [11.2. Modifying Stored Procedures](#112-modifying-stored-procedures)


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


### 2.1. Find Duplicates with RANK

Follow the steps below to clean out duplicates using `RANK`:
1. Check if duplicates exist using `GROUB BY` + `COUNT(*)`
2. Identify exact rows that are duplicated using `RANK OVER(PARTITION BY cols)`
   
   This actually devides the table into multiple partitions, one for each unique value of cols, then assigns a rank from 1 to the number of rows in the partition, then returns more than 1 if the row is duplicated.

**Example:**
```
id  show_name   actor_name
--  ---------   ----------
1   Office      Dwight
2   Office      Dwight
3   Office      Dwight
4   Family      Prichett
5   Family      Dunphy
6   Friends     Rachel
```

```sql
/* 1. Find duplicated show_name + actor_name */
SELECT show_name, actor_name, COUNT(*)
FROM actor
GROUP BY show_name, actor_name
HAVING COUNT(*) > 1
```

Output:
```
show_name   actor_name  count
---------   ----------  -----
Office      Dwight      2
```

```sql
/* 2. Find row with duplicated show_name */
SELECT * FROM (
    SELECT
        id, show_name,
        RANK() OVER(
            PARTITION BY show_name
            ORDER BY id) rk
    FROM actor) subquery
WHERE rk > 1
ORDER BY id
```

Output:
```
id  show_name   rk
--  ---------   --
2   Office      2
3   Office      3
5   Family      2
```

```sql
/* 2. Find row with duplicated show_name + actor_name */
SELECT * FROM (
    SELECT
        id, show_name,
        RANK() OVER(
            PARTITION BY show_name, actor_name
            ORDER BY id) rk
    FROM actor) subquery
WHERE rk > 1
```

Output:
```
id  show_name   rk
--  ---------   --
2   Office      2
```


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


<br>
<br>

****************
## 5. Grouping Sets

### 5.1. Grouping Sets
`GROUPING SETS` enable us to aggregate data multiple times for multiple groups in a single query.

```sql
SELECT
	column_1,
	column_2,
	SUM(column_3) as new_sum
FROM table_1
GROUP BY GROUPING SETS (
	(column_1), (column_1, column_2), ()
)
```

Every part of a `GROUPING SETS` list represents a different `GROUP BY` clause.
- (column_1) → `GROUP BY` column_1
- (column_1, column_2) → `GROUP BY` column_1, column_2
- () → Aggregate over all data

**Note:** `GROUPING SETS` is functionally similar to using `UNION`, meaning that it is like grouping the results one by one and using `UNION` to append them all. However, since `GROUPING SETS` only uses one query, it's more efficient.

### 5.2. Cube
`GROUPING SETS` allow us to specify different ways of grouping data in one query, but `CUBE` provides a shortcut to listing grouping sets one by one. `CUBE` generates the grouping set for a list of columns and <u>all possible subsets</u>.

```sql
SELECT
	column_1,
	column_2,
	SUM(column_3) as new_sum
FROM table_1
GROUP BY CUBE (column_1, column_2)

/*
Equivalent to:

SELECT
	…
GROUP BY GROUPING SETS (
(column_1, column_2),
(column_1), 
(column_2),
()
)
*/
```

### 5.3. Rollup
`ROLLUP` is another shorthand way of generating grouping sets which generates grouping sets of all listed columns. However, grouping sets for `ROLLUP` are considered <u>ordered</u> and <u>hierarchical</u>, unlike with `CUBE`.

```sql
SELECT
	column_1,
	column_2,
	column_3,
	SUM(column_4) as new_sum
FROM table_1
GROUP BY ROLLUP (column_1, column_2, column_3)

/*
Equivalent to:

SELECT
	…
GROUP BY GROUPING SETS (
(column_1, column_2, column_3),
(column_1, column_2), 
(column_1),
()
)
*/
```


<br>
<br>

****************
## 6. Schema Structures and Table Relationships

### 6.1. Information Schema
Postgres has a "meta" schema exposing the database structure called `information_schema`.

```sql
SELECT *
FROM information_schema.TABLE_NAME
```

`information_schema` has dozens of tables. A few useful ones are:
- `tables` → Data on tables (ex: name, schema, etc.)
- `columns` → Data on columns (ex: position, data type, maximum length, etc.)
- `table_constraints` → Data on constraints (ex: primary keys, foreign keys, etc.)

Check the documentation for more info:
https://www.postgresql.org/docs/12/information-schema.html


### 6.2. Comment
You can add comments to any database object using the `COMMENT` command:
- Tables
- Columns
- Schemas
- Databases
- Indexes
- etc.

```sql
COMMENT ON OBJECT object_name IS 'text';

COMMENT ON TABLE table_1 IS 'This is my table';

COMMENT ON COLUMN table_1.column_1 IS 'The first column of table_1';
```

**Note:** To remove a comment, simply set to `NULL`.

To view the comment on objects use this syntax:

```sql
SELECT obj_description(
'schema_name.table_name'::regclass
);

SELECT col_description(
'schema_name.table_name.column_name'::regclass, column_number
);
```

### 6.3. Adding and Dropping Constraints
Constraints restrict the data that can be added to your database by specifying criteria it must meet.

Basic types of constraints:
- `UNIQUE` → No duplicate values allowed in column.
- `NOT NULL` → No NULL values allowed in column.
- `CHECK` → Allows definition of expressions to check whether the data is acceptable or not.
```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name
CHECK (total_cost > 0);

ALTER TABLE table_name
ADD CONSTRAINT constraint_name
UNIQUE (column_1, column_2);
```

You can drop the constraint(s):
```sql
ALTER TABLE table_name
DROP CONSTRAINT constraint_name IF EXISTS;
```

In order to add `NOT NULL` constraint, the syntax is a little different:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name 
SET NOT NULL;
```

And to drop the `NOT NULL` constarint:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name
DROP NOT NULL;
```

To view the constraints:
```sql
SELECT *
FROM information_schema.table_constraints
WHERE table_schema = 'TABLE_SCHEMA'
    AND table_name = 'TABLE_NAME';
```


### 6.4. Adding and Dropping Foreign Keys
Although foreign keys can be created when a table is created, they can be added later as well.
```sql
ALTER TABLE child_table
ADD CONSTRAINT constraint_name
FOREIGN KEY (column_1)
REFERENCES parent_table (column_1);
```

Similar to other types of constraints, to drop the FK constraint we use the following syntax:
```sql
ALTER TABLE child_table
DROP CONSTRAINT constraint_name;
```


<br>
<br>

****************
## 7. Transactions
Transactions help protect database integrity and they are logical units of work in a database which are usually handled "behind the scenes". One transaction can contain multiple SQL statements, that can be any combination of `SELECT`, `INSERT`, `CREATE`, `UPDATE`.

Transactions are called ACID compliant: 
- Atomicity: transactions are "all or nothing"
- Consistency: successful transactions change DB state
- Isolation: transactions are independent from each other
- Durability: successful transactions persist after system failure


### 7.1. Update and Set
Updating data in a table/row in Postgres is done with the `UPDATE` command and the specific data to update and how they will be updated is stated in the `SET` command.

```sql
UPDATE table_name
	SET column_1 = new_value,
        column_2 = new_value_2
	WHERE …

UPDATE table_name
	SET column_1 = new_value
	WHERE id = 12345
```

### 7.2. Beginning and Ending Transactions

**Example:**
Defining transactions in Postgres requires us to define a starting point and an end point. We can start a transaction with `BEGIN` and end it with either `COMMIT` or `END`.
```sql
/*Begin the transaction*/
BEGIN TRANSACTION;

/*Update some of our data within the transaction*/
UPDATE physicians
    SET first_name = 'Bill',
        full_name = concat(last_name, ', Bill')
    WHERE id = 1;

/*End the transaction*/
END TRANSACTION;
/*Alternatively you can use COMMIT to end the transaction*/
COMMIT TRANSACTION;
```

**Note:** The `TRANSACTION` keyword is optional

We can interrupt or cancel a transaction with `ROLLBACK`.
```sql
/*Begin the transaction*/
BEGIN TRANSACTION;

/*Update some of our data within the transaction*/
UPDATE physicians
    SET first_name = 'Bill',
        full_name = concat(last_name, ', Bill')
    WHERE id = 1;

/*Cancel the changes*/
ROLLBACK;
```

### 7.3. Savepoint
Within transactions, we may want to save the state of a transaction at various points. Additionally, we may want to undo certain operations without aborting the entire transaction. Savepoints allow us to do both of these.

```sql
BEGIN;

/*Define the savepoint*/
SAVEPOINT savepoint_name;

/*Rollback to the savepoint if you want to undo the
SQL commands AFTER the savepoint*/
ROLLBACK TO savepoint_name;

/*If you don't want to ignore the commands after the
savepoint and want them to be effective*/
RELEASE savepoint_name;

COMMIT;
```

### 7.4. Database Locks
Database locks prevent users from modifying data that is being modified by a different transaction. Once the transaction is complete, the lock will be lifted.
Postgres handles table locking automatically, but we can use `LOCK TABLE` to lock tables manually.

```sql
BEGIN;

LOCK TABLE table_name --[lock_mode];

/*One or more SQL statements*/

END;
```

For more information on each lock mode, check out the documentation:
https://www.postgresql.org/docs/current/explicit-locking.html

**Example:**

```sql
BEGIN;

LOCK TABLE films IN SHARE ROW EXCLUSIVE MODE;

DELETE FROM films_user_comments WHERE id IN
    (SELECT id FROM films WHERE rating < 5);
DELETE FROM films WHERE rating < 5;

COMMIT;
```


<br>
<br>

****************
## 8. Table Inheritance and Partitioning

As database table sizes grow, we sometimes face performance issues. One way of solving this is with partitions. Partitions in Postgres allow us to divide a base table into smaller tables using:
- Ranges
- Lists
- A hash

### 8.1. Range Partitioning
In range partitioning, partitions are divided by non-overlapping intervals.

**Note:** 
- Indexes created on a partitioned table will cascade to all partitions
- `CHECK` and `NOT NULL` constraints are inherited by partitions
- `UNIQUE`/primary key constraints must include <u>all partitioning columns</u>
- Foreign keys work as usual on partitioned tables


**Example:**

```sql
/*Create the partitioned table*/
CREATE TABLE surgical_encounters_partitioned(
    surgery_id integer NOT NULL,
    master_patient_id integer NOT NULL,
    surgical_admission_date date NOT NULL,
    surgical_discharge_date date
) PARTITION BY RANGE (surgical_admission_date);


/*Create multiple partitions of the main table*/
CREATE TABLE surgical_encounters_y2016
    PARTITION OF surgical_encounters_partitioned
    FOR VALUES FROM ('2016-01-01') to ('2017-01-01'); -- Select the specific values
CREATE TABLE surgical_encounters_y2017
    PARTITION OF surgical_encounters_partitioned
    FOR VALUES FROM ('2017-01-01') to ('2018-01-01');
CREATE TABLE surgical_encounters_default
    PARTITION OF surgical_encounters_partitioned
    DEFAULT; -- Select data that are outside our explicitly defined partitions


/*[Optional] Create index on the partitioning columns*/
CREATE INDEX ON surgical_encounters_partitioned (surgical_admission_date);


/*Insert the data from the base table to our partitioned table*/
INSERT INTO surgical_encounters_partitioned
    SELECT
        surgery_id,
        master_patient_id,
        surgical_admission_date,
        surgical_discharge_date
    FROM surgical_encounters;
```

### 8.2. List Partitioning
Partitioning explicitly by lists is another way to partition tables in Postgres and it is useful when there are a small, known number of values for the partition field in the base table.


**Example:**

```sql
/*Create the partitioned table*/
CREATE TABLE departments_partitioned(
    hospital_id integer NOT NULL,
    department_id integer NOT NULL,
    department_name text,
    specialty_description text
) PARTITION BY LIST (hospital_id);


/*Create multiple partitions of the main table*/
CREATE TABLE departments_h111000
    PARTITION OF departments_partitioned
    FOR VALUES IN (111000); -- Select the data in a list of specific value(s)
CREATE TABLE departments_h112000
    PARTITION OF departments_partitioned
    FOR VALUES IN (112000);
CREATE TABLE departments_default
    PARTITION OF departments_partitioned
    DEFAULT; -- Select data that are outside our explicitly defined partitions


/*[Optional] Create index on the partitioning columns*/
CREATE INDEX ON departments_partitioned (hospital_id);
```

### 8.3. Hash Partitioning
Partitioning by hashes is useful when there is no obvious/natural way to divide your data. Hash partitioning is based on modular arithmetic. Ex:
- 5 mod 5 = 0
- 13 mod 5 = 3
- 1 mod 5 = 1


**Example:**

```sql
/*Create the partitioned table*/
CREATE TABLE orders_procedures_partitioned(
    orders_procedure_id integer NOT NULL,
    patient_encounter_id integer NOT NULL,
    ordering_provider_id integer REFERENCES physicians (id),
    order_cd text
) PARTITION BY HASH (orders_procedure_id, patient_encounter_id);


/*Create multiple partitions of the main table*/
CREATE TABLE orders_procedures_hash0
    PARTITION OF orders_procedures_partitioned
    FOR VALUES WITH (modulus 3, remainder 0);
CREATE TABLE orders_procedures_hash1
    PARTITION OF orders_procedures_partitioned
    FOR VALUES WITH (modulus 3, remainder 1);
CREATE TABLE orders_procedures_hash2
    PARTITION OF orders_procedures_partitioned
    FOR VALUES WITH (modulus 3, remainder 2);
```

In the exmple above if we take a look at the count of all rows from each partitioned table we see that the number of rows are distributed (somehow) equally.


### 8.4. Table Inheritance
Object-oriented programming (OOP) is a popular paradigm for writing software.
An important feature/concept in OOP is inheritance. Postgres provides table inheritance for constructing parent-child table relationships; similar (but not the same!!) as OOP inheritance.
```sql
CREATE TABLE parent (
	id INTEGER NOT NULL PRIMARY KEY
);

CREATE TABLE child (
	child_field TEXT
) INHERITS (parent);
```

```sql
-- Columns from parent, data from parent/child
SELECT *
FROM parent_table;

-- Columns from parent/child, data from child
SELECT *
FROM child_table;

-- Columns from parent, data from parent
SELECT *
FROM ONLY parent_table;
```

**Note:**
- Child tables can inherit from more than one parent table.
- `CHECK` and `NOT NULL` constraints are inherited.
- `UPDATE`, `DELETE`, and `INSERT` operations work differently depending on whether they're performed on parent or child tables.

|                    | `INSERT`                      | `UPDATE` | `DELETE` |
| ------------------ | ----------------------------- | -------- | -------- |
| Parent<br> (no `ONLY`)| - Data exists in parent table<br> - No effect on child table | - Data updated in table where it exists| - Data deleted in table where it exists|
| Child| - Data exists in child table<br> - Inherited column values visible in parent table| - Data updated in child table<br> - Changes visible in parent table| - Data deleted in child table<br> - Changes visible in parent table|

Limitations of table inheritance:
- Primary key, foreign keys, and indexes are not inherited by child tables.
- Data inserted in child table is <u>not routed</u> into parent table

Benefits of table inheritance:
- Performance
- Database management


<br>
<br>

****************
## 9. Views

A view is basically a stored SELECT query that can be used to simplify or replace complex queries, make data easier to use for end-users, or restrict data access.


### 9.1. Creating Views

```sql
CREATE VIEW my_view AS
	SELECT *
	FROM table_1 t1
	LEFT OUTER JOIN table_2 t2 
ON t1.join_condition = t2.join_condition 
	WHERE conditions IS NOT NULL;
```

### 9.2. Modifying and Deleting Views

```sql
-- Update/Change the query
CREATE OR REPLACE VIEW my_view AS
	SELECT …;
-- Delete the view
DROP VIEW IF EXISTS my_view;
-- Rename the view
ALTER VIEW IF EXISTS my_view
    RENAME TO my_new_view_name;
```

**Note:** It's always a good idea to use `IF EXISTS` in order to avoid any errors if the view is not available.

### 9.3. Updatable Views

By default, views in Postgres can be updated.
`INSERT`, `UPDATE`, and `DELETE` will work when certain conditions are met.

- The view mustn't conatin:
    - window functions
    - aggregate functions
    - `LIMIT`
    - `GROUP BY`, `HAVING`, etc.
    - any set operations (e.g. `UNION`)
- The view must be built on single table

To restrict the type of data that can be modified through the view, we can use `WITH CHECK OPTION`. This checks that data satisfies view constraints before modifying or inserting data and the data that fails this check will not be modified/inserted.

```sql
CREATE OR REPLACE VIEW sales_by_region AS 
SELECT region, SUM(sales) as total_sales
FROM orders
WHERE date_placed >= '2022-01-01'
GROUP BY region
WITH CHECK OPTION;
```

In this example, we are creating a view called "sales_by_region" that shows the total sales for each region, but only for orders that were placed on or after January 1st, 2022. The `WITH CHECK OPTION` clause ensures that any updates or inserts made through the view will also include the `WHERE` clause filter on "date_placed" column, so that the view will always only show sales for orders placed on or after January 1st, 2022.

For example, if an attempt is made to insert a row into the view with a "date_placed" value of "2021-12-31", the insert will be rejected because it does not meet the condition specified in the view's `SELECT` statement. Similarly, if an attempt is made to update a row in the view to change the "date_placed" value to a date before January 1st, 2022, the update will also be rejected.


### 9.4. Materialized Views

Views in Postgres do not store the underlying data from the `SELECT` statement by default. Sometimes, we need speed of access in addition to the convenience of a view.

The main advantage of using a materialized view is that it can greatly improve query performance. Because the view's results are pre-calculated and stored on disk, querying a materialized view is much faster than querying the underlying tables. This can be especially useful for large datasets, complex queries, or queries that need to be executed frequently.

Another advantage of using materialized views is that they can be used to provide real-time access to data. Because the materialized view is a separate table, it can be queried independently of the underlying tables, and updates to the underlying tables do not affect the materialized view. This can be useful in scenarios where the underlying tables are updated frequently and the view's results need to be available in real-time.

```sql
CREATE MATERIALIZED VIEW sales_by_product AS
SELECT product_id, SUM(quantity) as total_quantity, SUM(price) as total_sales
FROM orders
GROUP BY product_id
WITH DATA;
```

Once the materialized view is created, it can be queried just like any other table. Because the results of the materialized view are pre-calculated and stored on disk, this query will be much faster than querying the underlying "orders" table and calculating the sums on the fly.

Also, you can schedule a job or a task to refresh the materialized view with the data from the underlying tables. This way, the materialized view will always have the most recent data available.

```sql
REFRESH MATERIALIZED VIEW sales_by_product;
```

This command will update the materialized view with the new data from the underlying table, so it will always be up to date.

**Note:** `WITH DATA` option will immediately populate the materialized view with data, so it will be immediately available for querying, while `WITH NO DATA` will create the materialized view but it will not be populated with data and will not be available for querying until data is added to it using the `REFRESH` statement.

The `CONCURRENTLY` option is used when creating or refreshing a materialized view, it allows to perform the operation in a non-blocking way, which means that other queries can continue to run against the underlying tables while the materialized view is being created or refreshed. This can be useful in scenarios where the underlying tables are large or busy, and it's not possible or desirable to block other queries while the materialized view is being created or refreshed. It's also useful for the case where the materialized view is based on another materialized view, so it avoids the locking of the previous materialized view. In short, the CONCURRENTLY option allows to perform the operation in a non-blocking way, which can improve the performance and availability of the database for other queries.

```sql
CREATE MATERIALIZED VIEW sales_by_product AS
SELECT product_id, SUM(quantity) as total_quantity, SUM(price) as total_sales
FROM orders
GROUP BY product_id
WITH (concurrently=true);
```

```sql
REFRESH MATERIALIZED VIEW sales_by_product CONCURRENTLY;
```

Similar to views, you can drop materialized views using the syntax below:

```sql
DROP MATERIALIZED VIEW IF EXISTS my_view;
```


### 9.5. Recursive Views

Recursive views are a type of view that references itself in its own `SELECT` statement, allowing for the creation of a hierarchical data structure. They are used to work with hierarchical data, such as data that is organized into a tree or a graph.

For example, consider a table of employees with columns "employee_id" and "manager_id" which represents the reporting structure in an organization. A recursive view can be created to show the complete reporting hierarchy, where each row in the view represents an employee, and the "manager_id" column references the id of the employee's manager.

```sql
CREATE VIEW my_view AS
    WITH RECURSIVE employee_hierarchy (employee_id, manager_id, name) AS (
        -- non-recursive query
        SELECT employee_id, manager_id, name FROM employees
        WHERE manager_id IS NULL
        UNION
        -- recursive query
        SELECT e.employee_id, e.manager_id, e.name
        FROM employees e
        JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
    )
SELECT * FROM employee_hierarchy;
```

This can be also written like this:

```sql
CREATE RECURSIVE VIEW employee_hierarchy (employee_id, manager_id, name) AS
-- non-recursive query
SELECT employee_id, manager_id, name FROM employees
WHERE manager_id IS NULL
UNION
-- recursive query
SELECT e.employee_id, e.manager_id, e.name
FROM employees e
JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
```

**Note:** In the recursive view's syntax, we `UNION` or `UNION ALL` the non-recursive base query first and then have the recursive query second.

In this example, the recursive view "employee_hierarchy" is defined using a common table expression (CTE) which has a recursive part. The CTE first selects all the employees where the "manager_id" is null, which represents the top of the hierarchy and then for each employee, it will select all the employees who have the current employee's id as manager_id. This allows for the creation of a hierarchical data structure, where each row has a parent-child relationship with another row in the table. And the final `SELECT` statement retrieves all the rows from the "employee_hierarchy" CTE.

In summary, Recursive views are a powerful tool for working with hierarchical data, they allow a more natural and intuitive representation of hierarchical data, they simplify the task of querying and manipulating hierarchical data, and they help in writing queries that traverse the hierarchy.


<br>
<br>

****************
## 10. User-Defined Functions

PostgreSQL user-defined functions (UDFs) are a powerful feature that allows developers to create custom functions in the database. These functions can be used to perform complex calculations, manipulate data, or perform other tasks that are not provided by the built-in functions in PostgreSQL.

### 10.1. Creating Functions

**Note:** UDFs can be written in a variety of languages, including SQL, PL/pgSQL, PL/Tcl, and PL/Perl. The most common language used for UDFs is PL/pgSQL, which is a procedural language similar to PL/SQL in Oracle.

Here is an example of a simple UDF written in PL/pgSQL that calculates the square of a given number:

```sql
CREATE FUNCTION square(x INTEGER)
/*
This line is the start of the syntax to create a new function named "square" and it takes in a single parameter "x" of data type INTEGER.
*/
RETURNS INTEGER
/*
This line specifies that the function will return a value of data type INTEGER.
*/
AS $$
BEGIN
/*
This line starts the body of the function and the "AS $$" is used to indicate the beginning of the code block.
*/
  RETURN x * x;
/*
This line is the actual logic of the function, where it calculates the square of the input parameter "x" by multiplying it with itself.
*/
END;
$$ LANGUAGE plpgsql;
/*
This line indicates the end of the function's code block and "END;" is used to indicate the end of the function's logic. "$$ LANGUAGE plpgsql" is used to specify that this function is written in PL/pgSQL language.
*/
```

This function can be called in a SQL statement like this:

```sql
SELECT square(5);
```

This will return the value 25.

`DECLARE` is used in PL/pgSQL functions to define variables that will be used within the function. Variables defined with `DECLARE` are only visible within the function and are not accessible outside of it. It is used to define the variable's data type and an optional initial value. Once the variable is declared, it can be used in the function's logic to store and manipulate data. For example, it can be used to store the result of a query, perform calculations, or hold temporary values.

Another example of a UDF is one that takes in a table name and returns the number of rows in that table:

```sql
CREATE OR REPLACE FUNCTION count_rows(table_name TEXT)
RETURNS INTEGER AS $$
DECLARE
  row_count INTEGER;
BEGIN
  EXECUTE 'SELECT COUNT(*) FROM ' || table_name INTO row_count;
  RETURN row_count;
END;
$$ LANGUAGE plpgsql;
```

This function can be called in a SQL statement like this:

```sql
SELECT count_rows('employees');
```

This will return the number of rows in the 'employees' table.

UDFs can also take in multiple parameters, return multiple values, and even use dynamic SQL. They can also be used in conjunction with other SQL statements, such as `SELECT`, `INSERT`, `UPDATE`, and `DELETE`.


There are several ways to call a user-defined function (UDF) in PostgreSQL:

- Using the SELECT statement: This is the most common way to call a UDF. The UDF is included in the SELECT statement, along with any other columns or expressions that are needed. For example:

    ```sql
    SELECT square(5);
    ```

- Using the CALL statement: This method is used to call a UDF that returns no value, such as a function that only performs an action or updates data. The CALL statement is followed by the name of the UDF and any required parameters. For example:

    ```sql
    CALL update_employee_salary(5, 1000);
    ```

- Using the EXECUTE statement: This method is used to execute a UDF and store its result in a variable. The EXECUTE statement is followed by the name of the UDF and any required parameters, as well as the INTO keyword and the variable name. For example:

    ```sql
    EXECUTE 'SELECT square('||x||')' INTO result;
    ```

There are three noatations for defining UDF's parameters:
- Positional
```sql
SELECT f_function(1, 3, 12);
```
- Named
```sql
SELECT f_function(arg1 => 1, arg2 => 3, arg3 => 12);
```
- Mixed
```sql
SELECT f_function(1, arg2 => 3, arg3 => 12); -- Positional can't follow named
```

### 10.2. Modifying Functions

Dropping and modifying user-defined functions is very similar to dropping or modifying views.

```sql
DROP FUNCTION IF EXISTS f_function;
```

```sql
CREATE OR REPLACE FUNCTION f_function(arg1 arg_type, arg2 arg_type);
```

**Note:** `CREATE OR REPLACE` has limitations:
- Can't change name assigned to input parameters
- Can't change name of output parameter if more than 1
- Can add name to <u>unnamed inputs</u>


`ALTER FUNCTION` allows us to do a few things:
- Rename function
- Change owner
- Change schema
- Set or reset system settings, for example: change the search_path of the function
- Define dependencies

```sql
-- Rename function 
ALTER FUNCTION square RENAME TO power_of_two;

-- Change owner 
ALTER FUNCTION power_of_two(x INTEGER) OWNER TO new_owner;

-- Change schema
ALTER FUNCTION new_schema.power_of_two(x INTEGER) SET SCHEMA new_schema;

-- Set search_path
ALTER FUNCTION new_schema.power_of_two(x INTEGER) SET search_path = my_schema;

-- Define dependencies
ALTER FUNCTION new_schema.power_of_two(x INTEGER) ADD DEPENDENCY my_table;
```


<br>
<br>

****************
## 11. Stored Procedures

A stored procedure in PostgreSQL is a reusable routine that can accept input parameters and return a set of rows or a single value. They are similar to functions, but they can also perform actions that modify the database, such as inserting, updating, or deleting data. Stored procedures are written in PL/pgSQL, which is a procedural language designed specifically for PostgreSQL.

### 11.1. Creating and Calling Stored Procedures

The syntax for creating a stored procedure is similart to creating functions. Here are some examples of how to create and use stored procedures in PostgreSQL:

1. Creating a simple stored procedure:
    ```sql
    CREATE OR REPLACE PROCEDURE my_proc()
    AS $$
    BEGIN
        RAISE NOTICE 'Hello, world!';
    END;
    $$ LANGUAGE plpgsql;
    ``` 
    This stored procedure simply prints the message "Hello, world!" when it is executed.

2. Creating a stored procedure with input parameters:
    ```sql
    CREATE OR REPLACE PROCEDURE add_numbers(IN num1 INT, IN num2 INT)
    AS $$
    BEGIN
        RAISE NOTICE 'The sum of % and % is %', num1, num2, num1 + num2;
    END;
    $$ LANGUAGE plpgsql;
    ```
    This stored procedure takes in two integers as input parameters and calculates the sum of them, then prints out the result.

3. Creating a stored procedure that returns a set of rows:
    ```sql
    CREATE OR REPLACE PROCEDURE get_employees()
    RETURNS TABLE (id INT, name TEXT, salary NUMERIC)
    AS $$
    BEGIN
        RETURN QUERY SELECT id, name, salary FROM employees;
    END;
    $$ LANGUAGE plpgsql;
    ```
    This stored procedure returns a table of rows containing the id, name, and salary of all employees in the "employees" table.

4. Create a stored procedure that drops and recreates a table:
    ```sql
    CREATE OR REPLACE PROCEDURE recreate_table()
    AS $$
    BEGIN
        DROP TABLE IF EXISTS test_table();
        CREATE TABLE test_table(
            id INT
        )
    END;
    $$ LANGUAGE plpgsql;
    ```

To call or execute a stored procedure, you can use the `CALL` statement:

```sql
CALL my_proc();
CALL add_numbers(3, 5);
SELECT * FROM get_employees();
```

**Note:** Stored procedures can also be used in Triggers, and help to maintain Data Integrity and also can be used to perform complex queries.

There are several reasons why we might want to use stored procedures instead of functions in PostgreSQL:

1. **Data modification:** Stored procedures can perform actions that modify the database, such as inserting, updating, or deleting data, while functions can only return a value or a set of rows.
2. **Concurrency control:** Stored procedures can include explicit transaction control statements, such as COMMIT and ROLLBACK, which allows you to manage concurrent access to the database. Functions, on the other hand, are not allowed to modify the data and thus can not be used to control concurrent access to the data.
3. **Performance:** Stored procedures can be faster than running multiple separate SQL statements, because the database engine can optimize the execution of a stored procedure as a single unit. This can be especially beneficial for complex queries or operations that involve multiple tables.
4. **Security:** Stored procedures can be used to implement fine-grained access control, by granting or revoking execute privileges on specific stored procedures rather than on tables or views. This allows you to control which users can perform certain actions on the data.
5. **Code Reusability:** Stored procedures can be reused across multiple applications and can be called with different parameters, thus it's a good way to maintain the DRY principle.
6. **Complex Queries:** Stored procedures can help to perform complex queries and operations that involve multiple tables and joins, and return the result in a more organized way.

### 11.2. Modifying Stored Procedures

Modifying a stored procedure in PostgreSQL is similar to functions and view and it's a two-step process:
1. Drop the existing stored procedure
2. Create a new version of the stored procedure with the desired changes

Here is an example of how to modify a stored procedure called "my_proc":

```sql
-- Drop the existing stored procedure
DROP PROCEDURE IF EXISTS my_proc;

-- Create a new version of the stored procedure with the changes
CREATE OR REPLACE PROCEDURE my_proc()
AS $$
BEGIN
    -- New logic or changes here
    RAISE NOTICE 'Hello, modified world!';
END;
$$ LANGUAGE plpgsql;
```

You can also use `ALTER PROCEDURE` statement to modify the Stored Procedures.

```sql
ALTER PROCEDURE my_proc()
AS $$
BEGIN
    -- New logic or changes here
    RAISE NOTICE 'Hello, modified world!';
END;
$$ LANGUAGE plpgsql;
```


<br>
<br>

****************