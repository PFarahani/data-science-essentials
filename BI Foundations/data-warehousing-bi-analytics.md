# Getting Started with Data Warehousing and BI Analytics <!-- omit from toc -->


## Table of Contents <!-- omit from toc -->

- [1. An Introduction to Data Warehouses, Data Marts, and Data Lakes](#1-an-introduction-to-data-warehouses-data-marts-and-data-lakes)
  - [1.1. Data Warehouse Overview](#11-data-warehouse-overview)
  - [1.2. Data Marts Overview](#12-data-marts-overview)
  - [1.3. Data Lakes Overview](#13-data-lakes-overview)


<br>
<br>

****************
## 1. An Introduction to Data Warehouses, Data Marts, and Data Lakes

### 1.1. Data Warehouse Overview

A data warehouse is a system that gathers data from multiple sources into a single, central, and consistent data store to support various data analytics requirements.

**Data Warehouse Analytics**: Data warehouse systems enable data mining, artificial intelligence, and machine learning applications. The Extract, Transform, Load (ETL) process facilitates fast front-end reporting, delivering critical information quickly. Online Analytical Processing (OLAP) allows for fast, flexible, multidimensional data analysis in business intelligence and decision support applications.

**Data Warehouse Deployment**: Data warehouses have traditionally been hosted *on-premises* within enterprise data centers. In recent times, *Cloud Data Warehouses (CDWs)* have gained popularity, offering scalable, pay-as-you-go services accessible via the cloud.

**Benefits of Data Warehouse**: The key benefits of data warehouses include:
- Centralizing data from disparate sources into a single, reliable data store.
- Improving data quality through data integration and standardization.
- Enhancing data access performance for faster business insights.
- Enabling large-scale BI functions like data mining, AI, and machine learning, leading to smarter decision-making.
- Offering organizations the means to realize competitive advantages and gains through improved data quality and faster insights.

**Popular Data Warehouse Vendors and Offerings**:
1. **Oracle**: Offers Oracle Exadata for on-premises or Oracle Public Cloud deployment.
2. **IBM**: Provides IBM Netezza for cloud deployment on IBM Cloud, AWS, Azure, and private clouds.
3. **Amazon**: Offers Amazon RedShift with proprietary hardware and software in the cloud.
4. **Snowflake**: A multi-cloud analytics solution compliant with data privacy regulations.
5. **Google**: Provides Google BigQuery as a flexible, multi-cloud data warehouse solution.
6. **Microsoft**: Offers Azure Synapse Analytics with code-free visual ETL/ELT processes and support for both cloud and on-premises deployment.
7. **Teradata**: Offers Vantage, a multi-cloud data platform for enterprise analytics with unified data lakes and warehouses.
8. **Vertica**: Provides a hybrid-cloud data warehouse system with multi-cloud support and fast data transfer rates.
9. **IBM**: Offers Db2 Warehouse with scalability, parallel processing, and containerized scale-out capabilities.
10. **Oracle**: Provides Autonomous Data Warehouse in Oracle Public Cloud and on-premises with automated security features.

**Data Warehouse Evaluation Criteria**: Organizations use various criteria to evaluate data warehouse systems, including:
1. **Location**: Data warehouses can exist on-premises, on appliances, or on one or more cloud locations. Considerations include data security, privacy requirements, and data access speed.
2. **Features and Capabilities**: Evaluate architecture, scalability, data types supported (e.g., structured, semi-structured, unstructured), and batch/streaming data processing.
3. **Ease of Implementation**: Consider data governance, data migration, data transformation capabilities, and system optimization.
4. **User Management**: Implement a zero-trust security policy, manage and validate system users, and have notifications and reports to address issues promptly.
5. **Ease of Use and Skills**: Assess whether the staff has the skills to implement the chosen technology and the support from the implementation partner.
6. **Support Considerations**: Verify vendor support hours, channels, and the availability of service level agreements (SLAs) for uptime, security, and scalability.
7. **Cost Considerations**: Calculate the total cost of ownership (TCO), including infrastructure, software licensing, data migration, administration, and recurring support and maintenance costs.

**On-Premises vs. Public Cloud**: Organizations might choose on-premises installations for strict data security and privacy requirements. Public cloud options offer benefits like economies of scale, powerful compute power, and flexible price-for-performance options.

**Total Cost of Ownership (TCO)**: When evaluating costs, consider not only the initial expenses but also the TCO for running the system over several years, including infrastructure, software licensing, data migration, administration, and support and maintenance costs.


### 1.2. Data Marts Overview

A data mart is an isolated part of a larger enterprise data warehouse, built to serve a specific business function, purpose, or user community. It provides specific support for tactical decision-making.

**Data Mart Structure**: Data marts are designed to provide relevant data efficiently, saving time for end users and supporting tactical decision-making. They have a *relational database structure* with a *star or snowflake schema*, consisting of a *central fact table surrounded by dimension tables* for context.

**Comparison to Other Data Repositories**:

| Data Marts                                                         | Databases                                             |
| ------------------------------------------------------------------ | ----------------------------------------------------- |
| OLAP systems optimized for read-intensive queries                  | OLTP systems optimized for write-intensive operations |
| Use transactional databases (Txn DB) or warehouses as data sources | Use operational applications as sources of data       |
| Contain clean, validated analytical data                           | Contain raw, unprocessed transactional data           |
| Accumulate historical data for trend analysis                      | May not retain older data                             |

| Data Marts                               | Data Warehouses                                |
| ---------------------------------------- | ---------------------------------------------- |
| Small data warehouse with tactical scope | Large repositories with broad, strategic scope |
| Lean and fast                            | Large and slow                                 |


**Types of Data Marts**:
1. **Dependent Data Marts**: Draw data from the enterprise data warehouse and offer analytical capabilities within its restricted scope, inheriting the security of the warehouse.
2. **Independent Data Marts**: Bypass the data warehouse and directly source data from operational systems or external sources, requiring custom ETL pipelines and separate security measures.
3. **Hybrid Data Marts**: Partially depend on the enterprise data warehouse and combine inputs from data warehouses and external systems.

**Purpose and Advantages**:
- Provide relevant data to end-users when needed.
- Accelerate business processes with efficient query response times.
- Enable data-driven decision-making in a cost-efficient manner.
- Ensure secure access and control over data.


### 1.3. Data Lakes Overview

A data lake is a storage repository that can store large amounts of structured, semi-structured, and unstructured data in its raw or native format. Each data element is given a unique identifier and tagged with metadata, making it a flexible and agile repository.

**Benefits of Data Lakes**:
1. **Versatility**: Data lakes can store all types of data, including unstructured, semi-structured, and structured data from different sources.
2. **Scalability**: Data lakes can scale from terabytes to petabytes of data, making them suitable for handling massive datasets.
3. **Time Savings**: Data lakes save time by not requiring data to be pre-defined, structured, or transformed before loading.
4. **Data Reuse**: Data lakes allow fast and flexible reuse of data for various current and future use cases.

**Comparison to Data Warehouses**:
- **Data Integration**: Data lakes store data in its raw and unstructured form, while data warehouses require data to be processed and conformed to standards before loading.
- **Schema Definition**: Data lakes do not require a predefined schema, whereas data warehouses need a schema designed and implemented before loading data.
- **Data Quality and Governance**: Data lakes may have raw and uncurated data, whereas data warehouses have curated data adhering to data governance guidelines.
- **Users**: Data scientists, data developers, and machine learning engineers typically use data lakes, while business analysts and data analysts use data warehouses.

**Deployment Technologies**: Data lakes can be deployed using various technologies, including cloud object storage like Amazon S3, large-scale distributed systems like Apache Hadoop, relational database management systems, and NoSQL data repositories.

**Use Cases**: Data lakes can be used as self-serve staging areas for various use cases, including machine learning development and advanced analytics.

Overall, data lakes were created to address the limitations of data warehouses, and depending on an organization's requirements, both data warehouses and data lakes may be needed as they serve different needs. Data lakes provide a flexible and agile repository for raw and diverse data, enabling organizations to harness the full potential of their data for various analytics and decision-making tasks.