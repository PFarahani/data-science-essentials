# SQL Optimization Techniques <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->

- [1. Using `REGEXP_LIKE` to Replace `LIKE` Clauses](#1-using-regexp_like-to-replace-like-clauses)
- [2. Using `REGEXP_EXTRACT` to Replace `CASE-WHEN LIKE`](#2-using-regexp_extract-to-replace-case-when-like)
- [3. Converting Long Lists of `IN` Clauses into a Temporary Table](#3-converting-long-lists-of-in-clauses-into-a-temporary-table)
- [4. Ordering JOINs from Largest to Smallest Tables](#4-ordering-joins-from-largest-to-smallest-tables)
- [5. Using Simple Equi-Joins](#5-using-simple-equi-joins)
- [6. Grouping by Attributes with the Largest Number of Unique Values](#6-grouping-by-attributes-with-the-largest-number-of-unique-values)
- [7. Avoiding Subqueries in `WHERE` Clauses](#7-avoiding-subqueries-in-where-clauses)
- [8. Using `MAX` Instead of `RANK`](#8-using-max-instead-of-rank)
- [9. Additional Optimization Tips](#9-additional-optimization-tips)

---
## 1. Using `REGEXP_LIKE` to Replace `LIKE` Clauses
**Inefficient Query:**
```sql
SELECT *
FROM table1
WHERE lower(item_name) LIKE '%samsung%' OR
      lower(item_name) LIKE '%xiaomi%' OR
      lower(item_name) LIKE '%iphone%' OR
      lower(item_name) LIKE '%huawei%'
```

**Optimized Query:**
```sql
SELECT *
FROM table1
WHERE REGEXP_LIKE(lower(item_name), 'samsung|xiaomi|iphone|huawei')
```

## 2. Using `REGEXP_EXTRACT` to Replace `CASE-WHEN LIKE`
**Inefficient Query:**
```sql
SELECT
CASE
    WHEN concat(' ', item_name, ' ') LIKE '%acer%' THEN 'Acer'
    WHEN concat(' ', item_name, ' ') LIKE '%advance%' THEN 'Advance'
    WHEN concat(' ', item_name, ' ') LIKE '%alfalink%' THEN 'Alfalink'
    …
AS brand
FROM item_list
```

**Optimized Query:**
```sql
SELECT
    regexp_extract(item_name, '(asus|lenovo|hp|acer|dell|zyrex|...)') AS brand
FROM item_list
```

## 3. Converting Long Lists of `IN` Clauses into a Temporary Table
**Inefficient Query:**
```sql
SELECT *
FROM Table1 as t1
WHERE itemid IN (3363134, 5189076, ..., 4062349)
```

**Optimized Query:**
```sql
SELECT *
FROM Table1 as t1
JOIN (
    SELECT itemid
    FROM (
        SELECT split('3363134, 5189076, ...', ', ') AS bar
    )
    CROSS JOIN UNNEST(bar) AS t(itemid)
) AS Table2 as t2
ON t1.itemid = t2.itemid
```

## 4. Ordering JOINs from Largest to Smallest Tables
**Inefficient Query:**
```sql
SELECT *
FROM small_table
JOIN large_table
ON small_table.id = large_table.id
```

**Optimized Query:**
```sql
SELECT *
FROM large_table
JOIN small_table
ON small_table.id = large_table.id
```

## 5. Using Simple Equi-Joins
**Inefficient Query:**
```sql
SELECT *
FROM table1 a
JOIN table2 b
ON a.date = CONCAT(b.year, '-', b.month, '-', b.day)
```

**Optimized Query:**
```sql
SELECT *
FROM table1 a
JOIN (
    SELECT name, CONCAT(year, '-', month, '-', day) AS date
    FROM table2
) new
ON a.date = new.date
```

## 6. Grouping by Attributes with the Largest Number of Unique Values
**Inefficient Query:**
```sql
SELECT main_category, sub_category, itemid, sum(price)
FROM table1
GROUP BY main_category, sub_category, itemid
```

**Optimized Query:**
```sql
SELECT main_category, sub_category, itemid, sum(price)
FROM table1
GROUP BY itemid, sub_category, main_category
```

## 7. Avoiding Subqueries in `WHERE` Clauses
**Inefficient Query:**
```sql
SELECT sum(price)
FROM table1
WHERE itemid IN (SELECT itemid FROM table2)
```

**Optimized Query:**
```sql
WITH t2 AS (
    SELECT itemid
    FROM table2
)
SELECT sum(price)
FROM table1 t1
JOIN t2 ON t1.itemid = t2.itemid
```

## 8. Using `MAX` Instead of `RANK`
**Inefficient Query:**
```sql
SELECT *
FROM (
    SELECT userid, rank() OVER (ORDER BY prdate DESC) AS rank
    FROM table1
)
WHERE ranking = 1
```

**Optimized Query:**
```sql
SELECT userid, MAX(prdate)
FROM table1
GROUP BY userid
```

## 9. Additional Optimization Tips
- **Use `approx_distinct()`** instead of `count(distinct)` for very large datasets.
- **Use `approx_percentile(metric, 0.5)`** for calculating the median.
- **Avoid `UNIONs`** where possible.
- **Use `WITH` statements** instead of nested subqueries.
