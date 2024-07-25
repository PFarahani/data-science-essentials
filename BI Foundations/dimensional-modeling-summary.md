# Dimensional Modeling - Important Concepts <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [1. Introduction to Dimensional Modeling](#1-introduction-to-dimensional-modeling)
- [2. Star Schema](#2-star-schema)
- [3. Snowflake Schema](#3-snowflake-schema)
- [4. Fact Table](#4-fact-table)
- [5. Dimension Table](#5-dimension-table)
- [6. Grain](#6-grain)
- [7. Surrogate Key](#7-surrogate-key)
- [8. Slowly Changing Dimensions (SCD)](#8-slowly-changing-dimensions-scd)
- [9. Conformed Dimensions](#9-conformed-dimensions)
- [10. Junk Dimension](#10-junk-dimension)
- [11. Role-Playing Dimensions](#11-role-playing-dimensions)
- [12. Factless Fact Table](#12-factless-fact-table)
- [13. Aggregate Fact Table](#13-aggregate-fact-table)
- [14. Bridge Table](#14-bridge-table)
- [15. Degenerate Dimension](#15-degenerate-dimension)
- [16. ETL (Extract, Transform, Load)](#16-etl-extract-transform-load)
- [17. Bus Architecture](#17-bus-architecture)
- [18. Data Mart](#18-data-mart)
- [19. Grain Declaration](#19-grain-declaration)
- [20. Dimensional Hierarchy](#20-dimensional-hierarchy)
- [21. Multivalued Dimensions](#21-multivalued-dimensions)
- [22. Outrigger Dimension](#22-outrigger-dimension)
- [23. Mini-Dimension](#23-mini-dimension)
- [24. Data Stewardship](#24-data-stewardship)
- [25. Surrogate Key Pipeline](#25-surrogate-key-pipeline)
- [26. Slowly Changing Measure](#26-slowly-changing-measure)
- [27. Fact Table Grain](#27-fact-table-grain)
- [28. Snapshot Fact Table](#28-snapshot-fact-table)
- [29. Accumulating Snapshot Fact Table](#29-accumulating-snapshot-fact-table)
- [30. Semi-Additive Measures](#30-semi-additive-measures)
- [31. Non-Additive Measures](#31-non-additive-measures)
- [32. ETL Staging Area](#32-etl-staging-area)
- [33. Data Quality](#33-data-quality)
- [34. Data Lineage](#34-data-lineage)
- [35. Business Process](#35-business-process)
- [36. Drill-Down Analysis](#36-drill-down-analysis)
- [37. Derived Table](#37-derived-table)
- [38. Fact Table Aggregation](#38-fact-table-aggregation)
- [39. Performance Tuning](#39-performance-tuning)
- [40. Data Warehouse Architecture](#40-data-warehouse-architecture)
- [41. Dimensional Integrity](#41-dimensional-integrity)
- [42. Late Arriving Data](#42-late-arriving-data)
- [43. Data Warehouse Lifecycle](#43-data-warehouse-lifecycle)
- [44. OLAP (Online Analytical Processing)](#44-olap-online-analytical-processing)
- [45. Data Warehouse Security](#45-data-warehouse-security)
- [46. Metadata Management](#46-metadata-management)
- [47. Data Archiving](#47-data-archiving)
- [48. Data Governance](#48-data-governance)
- [49. Data Warehouse Automation](#49-data-warehouse-automation)
- [50. Data Visualization](#50-data-visualization)


---

## 1. Introduction to Dimensional Modeling
- A data modeling technique optimized for data warehousing and decision support systems, focusing on ease of querying.

## 2. Star Schema
- A type of database schema that consists of one or more fact tables referencing any number of dimension tables.
- Example: A sales database where a fact table contains sales data, and dimension tables include information about products, customers, and time.

## 3. Snowflake Schema
- A more complex version of the star schema where dimension tables are normalized into multiple related tables.
- Example: A sales database where dimension tables such as products and customers are further split into related tables like product categories and customer demographics.

## 4. Fact Table
- Central table in a star schema that contains quantitative data for analysis.
- Example: A sales fact table that includes measures like sales revenue, quantity sold, and discount applied.

## 5. Dimension Table
- Tables that contain descriptive attributes (dimensions) related to the facts.
- Example: A product dimension table that includes product names, categories, and descriptions.

## 6. Grain
- The level of detail or granularity of the data stored in a fact table.
- Example: Daily sales data versus monthly sales data in a fact table.

## 7. Surrogate Key
- A unique identifier for each row in a dimension table, not derived from application data.
- Example: An auto-incremented integer used as the primary key in a customer dimension table.

## 8. Slowly Changing Dimensions (SCD)
- Techniques for managing and tracking changes in dimension table attributes over time.
  - Type 1 SCD: Overwrites old data with new data in a dimension table.
  - Type 2 SCD: Creates a new record with a new surrogate key when a change occurs in the dimension data.
  - Type 3 SCD: Adds new columns to a dimension table to track changes over time.

## 9. Conformed Dimensions
- Dimensions that are shared across multiple fact tables and/or data marts.
- Example: A date dimension table used across sales, inventory, and finance data marts.

## 10. Junk Dimension
- Combines low-cardinality flags and indicators into a single dimension table.
- Example: Combining boolean flags like "is_promotional" and "is_returned" into a single junk dimension table.

## 11. Role-Playing Dimensions
- A single physical dimension table used in different contexts within the schema.
- Example: A date dimension table used for "order date," "ship date," and "delivery date" in a sales schema.

## 12. Factless Fact Table
- A fact table that captures events or conditions with no associated numeric facts.
- Example: A table capturing student attendance records without any numerical measures.

## 13. Aggregate Fact Table
- A summarized fact table that improves query performance by reducing the amount of data processed.
- Example: A monthly sales summary table that aggregates daily sales data.

## 14. Bridge Table
- A table used to handle many-to-many relationships between fact and dimension tables.
- Example: A table linking customers to multiple sales regions they belong to.

## 15. Degenerate Dimension
- A dimension attribute stored in the fact table itself.
- Example: An order number stored directly in the sales fact table without a separate dimension table.

## 16. ETL (Extract, Transform, Load)
- The process of extracting data from source systems, transforming it, and loading it into a data warehouse.
- Example: Extracting sales data from transactional databases, transforming it to match the warehouse schema, and loading it into the data warehouse.

## 17. Bus Architecture
- A framework for organizing data marts and warehouses using conformed dimensions and facts.
- Example: Ensuring that all data marts use the same date dimension table to provide a consistent view of time across the organization.

## 18. Data Mart
- A subset of the data warehouse focused on a specific business area or department.
- Example: A sales data mart that contains sales-related data for analysis by the sales department.

## 19. Grain Declaration
- The process of defining the granularity of the fact table during design.
- Example: Deciding that the grain of the sales fact table will be at the daily transaction level.

## 20. Dimensional Hierarchy
- A structure that organizes dimension attributes into levels of granularity.
- Example: A time hierarchy with levels for year, quarter, month, and day.

## 21. Multivalued Dimensions
- Dimensions where attributes can have multiple values for a single entity.
- Example: A customer dimension where a customer can have multiple phone numbers or email addresses.

## 22. Outrigger Dimension
- A dimension table that is linked to another dimension table rather than directly to a fact table.
- Example: A product subcategory table linked to a product category table, which in turn is linked to the fact table.

## 23. Mini-Dimension
- A dimension table that captures rapidly changing attributes, separated from the main dimension table.
- Example: A mini-dimension for tracking changes in customer preferences separate from the main customer dimension table.

## 24. Data Stewardship
- The management and oversight of an organization's data assets to ensure data quality and governance.
- Example: Implementing policies and procedures for data entry, validation, and maintenance to ensure accurate and reliable data in the data warehouse.

## 25. Surrogate Key Pipeline
- The process of assigning surrogate keys to records as they are loaded into the data warehouse.
- Example: Generating unique integer surrogate keys for customer records during the ETL process.

## 26. Slowly Changing Measure
- Measures in a fact table that change over time and need to be tracked historically.
- Example: Tracking historical changes in product prices in the sales fact table.

## 27. Fact Table Grain
- The level of detail or granularity of the data stored in a fact table.
- Example: Storing sales data at the transaction level versus the daily summary level.

## 28. Snapshot Fact Table
- Captures the state of a process at a specific point in time.
- Example: A table capturing the month-end inventory levels for each product.

## 29. Accumulating Snapshot Fact Table
- Tracks the progress of a process over time, updating records as milestones are reached.
- Example: A table tracking the stages of an order from placement to shipment and delivery, with updates to the same record as the order progresses.

## 30. Semi-Additive Measures
- Measures that can be summed across some dimensions but not others.
- Example: Bank account balances that can be summed across time but not across accounts.

## 31. Non-Additive Measures
- Measures that cannot be summed across any dimension.
- Example: Ratios or percentages, such as profit margins, that cannot be summed meaningfully.

## 32. ETL Staging Area
- A temporary storage area used during the ETL process to hold data before it is transformed and loaded.
- Example: Using a staging database to store raw sales data extracted from transactional systems before transforming and loading it into the data warehouse.

## 33. Data Quality
- Ensuring the accuracy, completeness, and reliability of data in the data warehouse.
- Example: Implementing data validation checks during the ETL process to ensure accurate and consistent data.

## 34. Data Lineage
- Tracking the origin and transformations of data as it moves through the data warehouse.
- Example: Maintaining metadata that documents the source systems, transformations, and loading processes for each data element in the warehouse.

## 35. Business Process
- A series of activities or tasks that produce a specific outcome, often used as the basis for defining fact tables.
- Example: The order fulfillment process, which includes order placement, processing, shipment, and delivery.

## 36. Drill-Down Analysis
- The ability to navigate from summarized data to more detailed data.
- Example: Analyzing sales data by drilling down from monthly sales totals to daily sales transactions.

## 37. Derived Table
- A table created as the result of a query, often used to simplify complex joins and calculations.
- Example: Creating a derived table to calculate the average order value for each customer segment.

## 38. Fact Table Aggregation
- The process of summarizing detailed data in a fact table to improve query performance.
- Example: Aggregating daily sales data into monthly sales summaries to speed up reporting queries.

## 39. Performance Tuning
- Techniques used to optimize the performance of the data warehouse and its queries.
- Example: Indexing key columns in fact and dimension tables to improve query performance.

## 40. Data Warehouse Architecture
- The overall structure and organization of the data warehouse, including schemas, ETL processes, and data storage.
- Example: Implementing a three-tier architecture with staging, integration, and presentation layers.

## 41. Dimensional Integrity
- Ensuring consistency and accuracy of dimensions across the data warehouse.
- Example: Implementing referential integrity constraints to ensure dimension keys in fact tables match primary keys in dimension tables.

## 42. Late Arriving Data
- Data that arrives after the initial load of a fact table and needs to be integrated into the existing data.
- Example: Handling late arriving sales transactions that need to be added to a fact table after the end of the reporting period.

## 43. Data Warehouse Lifecycle
- The stages of development and maintenance of a data warehouse, from initial planning to ongoing management.
- Example: Following a lifecycle approach that includes requirements gathering, design, implementation, testing, and maintenance phases.

## 44. OLAP (Online Analytical Processing)
- A category of software tools that provide analysis of data stored in a database, often used in data warehousing

.
- Example: Using OLAP tools to create multi-dimensional views of sales data for analysis and reporting.

## 45. Data Warehouse Security
- Measures to protect the data warehouse from unauthorized access and ensure data privacy.
- Example: Implementing user authentication and access controls to secure sensitive data in the data warehouse.

## 46. Metadata Management
- The process of managing data about data, including data definitions, structures, and lineage.
- Example: Maintaining a metadata repository that documents the structure and transformations of data in the data warehouse.

## 47. Data Archiving
- The process of moving historical data from the data warehouse to an archive for long-term storage.
- Example: Archiving older sales data to free up space and improve performance in the active data warehouse.

## 48. Data Governance
- The overall management of data availability, usability, integrity, and security in an organization.
- Example: Implementing policies and procedures to ensure consistent and accurate data across the data warehouse and other data systems.

## 49. Data Warehouse Automation
- The use of tools and technologies to automate the design, development, and management of the data warehouse.
- Example: Using ETL automation tools to streamline the data extraction, transformation, and loading processes.

## 50. Data Visualization
- The presentation of data in graphical or visual formats to aid understanding and decision-making.
- Example: Creating dashboards and reports with charts and graphs to visualize sales trends and performance metrics.