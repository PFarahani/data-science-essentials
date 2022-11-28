<img src="assets/postgresql-logo.svg" width="250" height="250">

# PostgreSQL Summary (Advanced)

## Table of contents

1. [Subqueries and CTEs](#section1-subqueries-and-ctes)

2. [Window Functions]()

3. [Advanced JOIN Operations]()

4. [Set Operations]()

5. [Grouping Sets]()

6. [Schema Structures and Table Relationships]()

7. [Transactions]()

8. [Table Inheritance and Partioning]()

9. [Views]()

10. [SQL Functions]()

11. [Stored Procedures]()

12. [Triggers]()

13. [Useful Methods and Tools]()


<br>
<br>

****************
## Section1: Subqueries and CTEs

Different flavours of subqueries:
- Basic subquery - FROM clause
- Common table expressions/WITH clauses
- Comparisons with main data set


### Subqueries
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

### Common Table Expressions
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

### Subqueries for Comparisons
