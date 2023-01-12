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
- `UPDATE`, `DELETE`, and `INSERT` operations work differently depending on whether they’re performed on parent or child tables.

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