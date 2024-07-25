# SQL vs. PySpark: A Comprehensive Command Comparison <!-- omit from toc -->

This documentation provides a side-by-side comparison of commonly used SQL commands and their PySpark equivalents.

## Table of Contents <!-- omit from toc -->
- [1. Selection and Filtering](#1-selection-and-filtering)
- [2. Aggregation](#2-aggregation)
- [3. Joins](#3-joins)
- [4. Grouping](#4-grouping)
- [5. Sorting](#5-sorting)
- [6. Column Operations](#6-column-operations)
- [7. String Operations](#7-string-operations)
- [8. Date Functions](#8-date-functions)
- [9. Window Functions](#9-window-functions)
- [10. Table and Schema Operations](#10-table-and-schema-operations)

---

## 1. Selection and Filtering

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT column1, column2 FROM table;`                               | `df.select("column1", "column2")`                             | Select specific columns                |
| `SELECT * FROM table WHERE condition;`                              | `df.filter("condition")`                                      | Filter rows                            |
| `SELECT * FROM table WHERE column != 'value';`                      | `df.filter(df.column != 'value')`                             | Filter rows with inequality            |
| `SELECT * FROM table WHERE column IN ('value1', 'value2');`         | `df.filter(df.column.isin('value1', 'value2'))`               | Filter rows in list                    |
| `SELECT * FROM table WHERE column NOT IN ('value1', 'value2');`     | `df.filter(~df.column.isin('value1', 'value2'))`              | Filter rows not in list                |
| `SELECT * FROM table WHERE column IS NULL;`                         | `df.filter(df.column.isNull())`                               | Filter rows with null values           |
| `SELECT * FROM table WHERE column IS NOT NULL;`                     | `df.filter(df.column.isNotNull())`                            | Filter rows with non-null values       |

## 2. Aggregation

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT AVG(column) FROM table;`                                    | `df.select(F.avg("column"))`                                  | Average                                |
| `SELECT COUNT(*) FROM table;`                                       | `df.count()`                                                 | Count rows                             |
| `SELECT COUNT(DISTINCT column) FROM table;`                         | `df.select(F.countDistinct("column"))`                        | Count distinct values                  |
| `SELECT SUM(column) FROM table;`                                    | `df.select(F.sum(df.column))`                                 | Sum values                             |
| `SELECT MAX(column) FROM table;`                                    | `df.select(F.max(df.column))`                                 | Max value                              |
| `SELECT MIN(column) FROM table;`                                    | `df.select(F.min(df.column))`                                 | Min value                              |

## 3. Joins

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT * FROM table1 JOIN table2 ON table1.id = table2.id;`        | `df1.join(df2, df1.id == df2.id)`                             | Join tables                            |
| `SELECT * FROM table1 CROSS JOIN table2;`                           | `df1.crossJoin(df2)`                                          | Cross join                             |
| `SELECT t1.*, t2.* FROM table1 t1 JOIN table2 t2 ON t1.id = t2.id;` | `df1.alias("t1").join(df2.alias("t2"), F.col("t1.id") == F.col("t2.id"))` | Alias for table in join                |

## 4. Grouping

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT column, COUNT(*) FROM table GROUP BY column;`               | `df.groupBy("column").count()`                                | Group by                               |
| `SELECT column, COUNT(*) AS count FROM table GROUP BY column;`      | `df.groupBy("column").agg(F.count("*").alias("count"))`       | Group by with alias                    |
| `SELECT column, COUNT(*) FROM table GROUP BY column HAVING COUNT(*) > 1;` | `df.groupBy("column").count().filter(F.col("count") > 1)`     | Group by with having                   |

## 5. Sorting

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT * FROM table ORDER BY column ASC;`                          | `df.orderBy("column", ascending=True)`                        | Order by ascending                     |
| `SELECT * FROM table ORDER BY column DESC;`                         | `df.orderBy(df.column.desc())`                                | Order by descending                    |

## 6. Column Operations

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT *, (column1 + column2) AS new_column FROM table;`           | `df.withColumn("new_column", F.col("column1") + F.col("column2"))` | Add a new column                       |
| `SELECT column AS alias_name FROM table;`                           | `df.select(F.col("column").alias("alias_name"))`              | Column alias                           |
| `SELECT CAST(column AS datatype) FROM table;`                       | `df.select(F.col("column").cast("datatype"))`                 | Cast data type                         |
| `ALTER TABLE table DROP COLUMN column;`                             | `df.drop("column")`                                           | Drop column                            |
| `ALTER TABLE table RENAME COLUMN column1 TO column2;`               | `df.withColumnRenamed("column1", "column2")`                  | Rename column                          |
| `ALTER TABLE table ALTER COLUMN column TYPE new_type;`              | `df.withColumn("column", df["column"].cast("new_type"))`      | Change column type                     |
| `SELECT COALESCE(column, 'default_value') FROM table;`              | `df.select(F.coalesce(df.column, F.lit('default_value')))`     | Coalesce                               |

## 7. String Operations

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT UPPER(column) FROM table;`                                  | `df.select(F.upper(df.column))`                               | Upper case                             |
| `SELECT LOWER(column) FROM table;`                                  | `df.select(F.lower(df.column))`                               | Lower case                             |
| `SELECT LENGTH(column) FROM table;`                                 | `df.select(F.length(df.column))`                              | String length                          |
| `SELECT TRIM(column) FROM table;`                                   | `df.select(F.trim(df.column))`                                | Trim string                            |
| `SELECT LTRIM(column) FROM table;`                                  | `df.select(F.ltrim(df.column))`                               | Left trim string                       |
| `SELECT RTRIM(column) FROM table;`                                  | `df.select(F.rtrim(df.column))`                               | Right trim string                      |
| `SELECT REPLACE(column, 'find', 'replace') FROM table;`             | `df.select(F.regexp_replace(df.column, 'find', 'replace'))`   | Replace string                         |
| `SELECT CONCAT(column1, column2) AS new_column FROM table;`         | `df.withColumn("new_column", F.concat(F.col("column1"), F.col("column2")))` | Concatenate columns                    |
| `SELECT column1 || ' ' || column2 AS new_column FROM table;`        | `df.withColumn("new_column", F.concat_ws(' ', df.column1, df.column2))` | Concatenate with separator             |

## 8. Date Functions

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT EXTRACT(YEAR FROM date_column) FROM table;`                 | `df.select(F.year(F.col("date_column")))`                     | Extracting date parts                  |

## 9. Window Functions

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `SELECT AVG(column) OVER (PARTITION BY column2) FROM table;`        | `df.withColumn("avg", F.avg("column").over(Window.partitionBy("column2")))` | Average over partition                 |
| `SELECT SUM(column) OVER (PARTITION BY column2) FROM table;`        | `df.withColumn("sum", F.sum("column").over(Window.partitionBy("column2")))` | Sum over partition                     |
| `SELECT LEAD(column, 1) OVER (ORDER BY column2) FROM table;`        | `df.withColumn("lead", F.lead("column", 1).over(Window.orderBy("column2")))` | Lead function                          |
| `SELECT LAG(column, 1) OVER (ORDER BY column2) FROM table;`         | `df.withColumn("lag", F.lag("column", 1).over(Window.orderBy("column2")))` | Lag function                           |

## 10. Table and Schema Operations

| **SQL Command**                                                     | **PySpark Command**                                          | **Description**                        |
|---------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------|
| `CREATE TABLE new_table (column1 datatype, column2 datatype);`      | `df.write.saveAsTable("new_table")`                           | Create table                           |
| `DROP TABLE table;`                                                 | `spark.sql("DROP TABLE table")`                               | Drop table                             |
| `ALTER TABLE table ADD COLUMNS (column1 datatype, column2 datatype);` | `df.withColumn("column1", default_value)`                    | Add columns                            |
| `DESCRIBE TABLE table;`                                             | `df.printSchema()`                                            | Describe table schema                  |

