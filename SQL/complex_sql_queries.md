# Complex SQL Queries <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [1. Finding the Nth Maximum Salary](#1-finding-the-nth-maximum-salary)
- [2. Counting Columns in a Table](#2-counting-columns-in-a-table)
- [3. Using the EXISTS Clause](#3-using-the-exists-clause)
- [4. Finding Not Null Columns in a Table](#4-finding-not-null-columns-in-a-table)
- [5. Deleting Duplicate Rows in a Table](#5-deleting-duplicate-rows-in-a-table)
- [6. Finding the Maximum Salary without MAX Function](#6-finding-the-maximum-salary-without-max-function)
- [7. Alternative to DESC](#7-alternative-to-desc)
- [8. Examples of START WITH, CONNECT BY, and PRIOR](#8-examples-of-start-with-connect-by-and-prior)
- [9. Finding the Database Name](#9-finding-the-database-name)
- [10. Converting Numbers to Words](#10-converting-numbers-to-words)
- [11. Eliminating Duplicate Values in a Table](#11-eliminating-duplicate-values-in-a-table)
- [12. Generating Primary Key Values](#12-generating-primary-key-values)
- [13. Calculating Time Difference Between Two Dates](#13-calculating-time-difference-between-two-dates)
- [14. Counting Different Data Values in a Column](#14-counting-different-data-values-in-a-column)
- [15. Counting/Summing Ranges of Data Values in a Column](#15-countingsumming-ranges-of-data-values-in-a-column)
- [16. Retrieving the Nth Row from a Table](#16-retrieving-the-nth-row-from-a-table)
- [17. Retrieving Rows X to Y from a Table](#17-retrieving-rows-x-to-y-from-a-table)
- [18. Selecting Every Nth Row from a Table](#18-selecting-every-nth-row-from-a-table)
- [19. Selecting the Top N Rows from a Table](#19-selecting-the-top-n-rows-from-a-table)
- [20. Coding a Tree-Structured Query](#20-coding-a-tree-structured-query)
- [21. Implementing IF-THEN-ELSE in a SELECT Statement](#21-implementing-if-then-else-in-a-select-statement)
- [22. Examining the Exact Content of a Database Column](#22-examining-the-exact-content-of-a-database-column)
- [23. Dropping a Column from a Table](#23-dropping-a-column-from-a-table)
- [24. Renaming a Column in a Table](#24-renaming-a-column-in-a-table)
- [25. Changing Oracle Password](#25-changing-oracle-password)
- [26. Sending Messages to Different Sessions](#26-sending-messages-to-different-sessions)
- [27. Receiving Messages in Different Sessions](#27-receiving-messages-in-different-sessions)
- [28. Overloading Concept](#28-overloading-concept)
- [29. Restriction Concept](#29-restriction-concept)
- [30. Dynamic SQL](#30-dynamic-sql)
- [31. Frequently Asked Questions](#31-frequently-asked-questions)
  - [31.1. What is SQL?](#311-what-is-sql)
  - [31.2. What are the types of SQL statements?](#312-what-are-the-types-of-sql-statements)

---

## 1. Finding the Nth Maximum Salary

```sql
SELECT DISTINCT SAL 
FROM EMP A 
WHERE &N = (
    SELECT COUNT(DISTINCT B.SAL)
    FROM EMP B 
    WHERE A.SAL <= B.SAL
);
```

## 2. Counting Columns in a Table

```sql
SELECT COUNT(COLUMN_NAME) 
FROM USER_TAB_COLUMNS 
WHERE TABLE_NAME = 'DEPT';
```

## 3. Using the EXISTS Clause

```sql
SELECT DNAME, DEPTNO 
FROM DEPT 
WHERE EXISTS (
    SELECT * 
    FROM EMP 
    WHERE DEPT.DEPTNO = EMP.DEPTNO
);
```

## 4. Finding Not Null Columns in a Table

```sql
SELECT COLUMN_NAME 
FROM USER_TAB_COLUMNS 
WHERE NULLABLE = 'N' AND TABLE_NAME = 'COUNTRY';
```

## 5. Deleting Duplicate Rows in a Table

```sql
DELETE DEPT 
WHERE ROWID NOT IN (
    SELECT MAX(ROWID) 
    FROM DEPT 
    GROUP BY DEPTNO 
    HAVING COUNT(*) >= 1
);
```

## 6. Finding the Maximum Salary without MAX Function

Method 1:
```sql
SELECT DISTINCT SAL 
FROM EMP1 
WHERE SAL NOT IN (
    SELECT SAL 
    FROM EMP1 
    WHERE SAL < ANY (SELECT SAL FROM EMP1)
);
```

Method 2:
```sql
SELECT SAL 
FROM EMP 
WHERE SAL >= ALL (SELECT SAL FROM EMP);
```

## 7. Alternative to DESC

```sql
SELECT COLUMN_NAME NAME, 
       DECODE(NULLABLE, 'N', 'NOT NULL', 'Y', ' ') "NULL", 
       CONCAT(DATA_TYPE, DATA_LENGTH) TYPE 
FROM USER_TAB_COLUMNS 
WHERE TABLE_NAME = 'DEPT';
```

## 8. Examples of START WITH, CONNECT BY, and PRIOR

Example 1:
```sql
SELECT ENAME, JOB, LEVEL, EMPNO, MGR 
FROM EMP111 
CONNECT BY PRIOR EMPNO = MGR 
START WITH ENAME = 'RAJA';
```

Example 2:
```sql
SELECT EMPNO, LPAD(' ', 6 * (LEVEL - 1)) || ENAME "EMPLOYEE NAME" 
FROM EMP 
START WITH ENAME = 'KING' 
CONNECT BY PRIOR EMPNO = MGR;
```

## 9. Finding the Database Name

```sql
SELECT * FROM GLOBAL_NAME;
```

## 10. Converting Numbers to Words

```sql
SELECT TO_CHAR(TO_DATE(&NUM, 'J'), 'JSP') 
FROM DUAL;
```

## 11. Eliminating Duplicate Values in a Table

Method 1:
```sql
DELETE FROM table_name A 
WHERE ROWID > (
    SELECT MIN(ROWID) 
    FROM table_name B 
    WHERE A.key_values = B.key_values
);
```

Method 2:
```sql
CREATE TABLE table_name2 AS 
SELECT DISTINCT * FROM table_name1;
DROP TABLE table_name1;
RENAME table_name2 TO table_name1;
```

Method 3:
```sql
DELETE FROM my_table 
WHERE ROWID NOT IN (
    SELECT MAX(ROWID) 
    FROM my_table 
    GROUP BY my_column_name
);
```

Method 4:
```sql
DELETE FROM my_table t1 
WHERE EXISTS (
    SELECT 'x' 
    FROM my_table t2 
    WHERE t2.key_value1 = t1.key_value1 
      AND t2.key_value2 = t1.key_value2 
      AND t2.rowid > t1.rowid
);
```

## 12. Generating Primary Key Values

Method 1:
```sql
UPDATE table_name 
SET seqno = ROWNUM;
```

Method 2:
```sql
CREATE SEQUENCE sequence_name START WITH 1 INCREMENT BY 1;
UPDATE table_name 
SET seqno = sequence_name.NEXTVAL;
```

## 13. Calculating Time Difference Between Two Dates

```sql
SELECT FLOOR((date1 - date2) * 24 * 60 * 60) / 3600 || ' HOURS ' || 
       FLOOR(((date1 - date2) * 24 * 60 * 60 - FLOOR((date1 - date2) * 24 * 60 * 60 / 3600) * 3600) / 60) || ' MINUTES ' || 
       ROUND(((date1 - date2) * 24 * 60 * 60 - FLOOR((date1 - date2) * 24 * 60 * 60 / 3600) * 3600 - FLOOR(((date1 - date2) * 24 * 60 * 60 - FLOOR((date1 - date2) * 24 * 60 * 60 / 3600) * 3600) / 60) * 60)) || ' SECS ' time_difference 
FROM ...;
```

## 14. Counting Different Data Values in a Column

```sql
SELECT dept, 
       SUM(DECODE(sex, 'M', 1, 0)) MALE, 
       SUM(DECODE(sex, 'F', 1, 0)) FEMALE, 
       COUNT(DECODE(sex, 'M', 1, 'F', 1)) TOTAL 
FROM my_emp_table 
GROUP BY dept;
```

## 15. Counting/Summing Ranges of Data Values in a Column

Example 1:
```sql
SELECT f2, 
       COUNT(DECODE(GREATEST(f1, 59), LEAST(f1, 100), 1, 0)) "Range 60-100", 
       COUNT(DECODE(GREATEST(f1, 30), LEAST(f1, 59), 1, 0)) "Range 30-59", 
       COUNT(DECODE(GREATEST(f1, 29), LEAST(f1, 0), 1, 0)) "Range 00-29" 
FROM my_table 
GROUP BY f2;
```

Example 2:
```sql
SELECT ename "Name", 
       sal "Salary", 
       DECODE(TRUNC(f2 / 1000, 0), 0, 0.0, 1, 0.1, 2, 0.2, 3, 0

.3, 4, 0.4, 5, 0.5, 6, 0.6, 7, 0.7, 8, 0.8, 9, 0.9, 10, 1.0) "Deduction" 
FROM emp_table;
```

## 16. Retrieving the Nth Row from a Table

```sql
SELECT sal 
FROM (SELECT ROWNUM rno, sal 
      FROM (SELECT sal FROM emp ORDER BY sal DESC)) 
WHERE rno = &n;
```

## 17. Retrieving Rows X to Y from a Table

```sql
SELECT ename 
FROM (SELECT ROWNUM rno, ename 
      FROM (SELECT ename FROM emp ORDER BY ename)) 
WHERE rno BETWEEN &x AND &y;
```

## 18. Selecting Every Nth Row from a Table

```sql
SELECT * 
FROM emp 
WHERE ROWNUM < = &N * (SELECT COUNT(*) 
                       FROM emp) 
      AND MOD(ROWNUM, &N) = 0;
```

## 19. Selecting the Top N Rows from a Table

Method 1:
```sql
SELECT sal 
FROM emp 
WHERE ROWNUM < &N 
ORDER BY sal DESC;
```

Method 2:
```sql
SELECT ROWNUM, ename, sal 
FROM (SELECT * 
      FROM emp 
      ORDER BY sal DESC) 
WHERE ROWNUM <= &N;
```

## 20. Coding a Tree-Structured Query

Example 1:
```sql
SELECT empno, ename, job, mgr 
FROM emp 
START WITH ename = 'KING' 
CONNECT BY PRIOR empno = mgr;
```

Example 2:
```sql
SELECT ename, job, level 
FROM emp 
START WITH ename = 'KING' 
CONNECT BY PRIOR empno = mgr;
```

## 21. Implementing IF-THEN-ELSE in a SELECT Statement

```sql
SELECT ename, 
       sal, 
       DECODE(
           TRUNC(months_between(sysdate, hiredate)/12, 0), 
           0, sal*0.1, 
           1, sal*0.15, 
           2, sal*0.20, 
           3, sal*0.25, 
           4, sal*0.30, 
           5, sal*0.35, 
           6, sal*0.40, 
           7, sal*0.45, 
           8, sal*0.50, 
           9, sal*0.55, 
           10, sal*0.60
       ) "Increment" 
FROM emp;
```

## 22. Examining the Exact Content of a Database Column

```sql
SELECT DUMP(column_name) 
FROM table_name 
WHERE condition;
```

## 23. Dropping a Column from a Table

```sql
ALTER TABLE table_name 
DROP COLUMN column_name;
```

## 24. Renaming a Column in a Table

```sql
ALTER TABLE table_name 
RENAME COLUMN old_column_name TO new_column_name;
```

## 25. Changing Oracle Password

```sql
ALTER USER username IDENTIFIED BY new_password;
```

## 26. Sending Messages to Different Sessions

```sql
EXEC DBMS_PIPE.SEND_MESSAGE (pipe_name, message);
```

## 27. Receiving Messages in Different Sessions

```sql
EXEC DBMS_PIPE.RECEIVE_MESSAGE (pipe_name, message);
```

## 28. Overloading Concept

Overloading in PL/SQL allows the use of the same name for different subprograms provided their formal parameters differ in number or datatype.

## 29. Restriction Concept

Restrictions on usage can include the limited use of certain SQL constructs, limitations in PL/SQL subprograms, and adherence to certain coding standards.

## 30. Dynamic SQL

Dynamic SQL refers to the capability to build and execute SQL statements dynamically at runtime. It allows greater flexibility in SQL statement construction.

Example:
```sql
EXECUTE IMMEDIATE 'SELECT COUNT(*) FROM ' || table_name INTO v_count;
```

## 31. Frequently Asked Questions

### 31.1. What is SQL?
SQL (Structured Query Language) is the standard language for dealing with Relational Databases.

### 31.2. What are the types of SQL statements?
1. DDL (Data Definition Language)
2. DML (Data Manipulation Language)
3. DCL (Data Control Language)
4. TCL (Transaction Control Language)
