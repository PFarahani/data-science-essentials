<img src="assets/postgresql-logo.svg" width="250" height="250">

# PostgreSQL Summary (Advanced) <!-- omit from toc -->

## Table of contents <!-- omit from toc -->

- [1. Subqueries and CTEs](#1-subqueries-and-ctes)
  - [1.1. Subqueries](#11-subqueries)
  - [1.2. Common Table Expressions](#12-common-table-expressions)
  - [1.3. Subqueries for Comparisons](#13-subqueries-for-comparisons)


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
