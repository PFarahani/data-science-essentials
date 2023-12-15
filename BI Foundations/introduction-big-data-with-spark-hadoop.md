<img src="https://spark.apache.org/images/spark-logo-trademark.png" alt="Spark logo" height="110">
<img src="https://svn.apache.org/repos/asf/comdev/project-logos/originals/hadoop.svg" alt="Hadoop logo" height="110">

# Introduction to Big Data with Spark and Hadoop <!-- omit from toc -->


## Table of Contents <!-- omit from toc -->

- [1. Introduction to Big Data](#1-introduction-to-big-data)
  - [1.1. What is Big Data](#11-what-is-big-data)
    - [1.1.1. The Core V's of Big Data](#111-the-core-vs-of-big-data)
  - [1.2. Parallel Processing and Scalability for Big Data](#12-parallel-processing-and-scalability-for-big-data)
  - [1.3. Big Data Tools and Ecosystem](#13-big-data-tools-and-ecosystem)


<br>
<br>

****************
## 1. Introduction to Big Data


### 1.1. What is Big Data

Bernard Marr defines big data as the digital trace generated in the current digital era:

> "The basic idea behind the phrase 'Big Data' is that everything we do is increasingly leaving a digital trace (or data), which we can use and analyze to become smarter. The driving forces in this brave new world are access to ever-increasing volumes of data and our ever-increasing technological capability to mine that data for commercial insights."
>---Bernard Marr

**Big Data vs. Small Data:**

| Small Data                                                                                   | Big Data                                                                                 |
| -------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Small enough for human inference                                                             | Data generated in huge volumes and could be structured, semi-structured, or unstructured |
| Accumulated slowly                                                                           | Needs processing to generate insights for human consumption                              |
| Relatively consistent and structured data usually stored in known forms such as JSON and XML | Arrives continuously at enormous speed from multiple sources                             |
| Mostly located in storage systems within enterprises or data centers                         | Comprises any forms of data including video, photos, and more                            |


**Characteristics of Big Data:**

- **Volume:** Massive data generated continuously, stored in the Cloud or specialized server farms.
- **Variety:** In various forms like text, images, audio, and videos.
- **Structure:** Often lacks structure, requiring specialized programs for interpretation.

> "Big data is high-**volume**, high-**velocity** and/or high-**variety** information assets that demand cost-effective, innovative forms of information processing that enable enhanced insight, decision making, and process automation."
> --- Gartner

#### 1.1.1. The Core V's of Big Data

1. **Velocity:** Speed of data arrival, processed close to real-time or in batches.

    - **Description:**
      - Data that is generated fast
      - Process that never stops   
    - **Attributes:**
      - Batch
      - Close to real time
      - Streaming
    - **Drivers:**
      - Improved connectivity and hardware
      - Rapid response times

2. **Volume:** Increase in the amount of data stored over time.

    - **Description:**
      - Scale of data
      - Increased amount of stored data
    - **Attributes:**
      - Petabytes
      - Exa
      - Zetta
    - **Drivers:**
      - Increase in data sources
      - Higher resolution sensors
      - Scalable hardware infrastructure

3. **Variety:** Diversity in data forms.

    - **Description:**
      - Data that comes from machines, people, and processes
      - Structured, semi structured, and unstructured data
    - **Attributes:**
      - Structure, complexity, and origin
    - **Drivers:**
      - Mobile technologies
      - Scalable infrastructure
      - Resilience
      - Fault recovery
      - Efficient storage and retrieval

4. **Veracity:** Quality, origin, accuracy, and consistency of data.

    - **Description:**
      - Quality, origin, and conformity of facts
      - Accuracy of data
      - Data that comes from people and processes
    - **Attributes:**
      - Consistency and completeness
      - Ambiguity
      - Ambiguity
    - **Drivers:**
      - Cost and traceability
      - Robust insgestion
      - ETL mechanisms

**Note:** The fifth V of big data is **Value** which is the outcome of making intelligent business decisions from leveraging the previous four V's. The ultimate organizational goals is to produce values in the form of faster and smarter decisions, efficient resource use, and discovering new opportunities. Big Data supports innovation and thus creates value.


### 1.2. Parallel Processing and Scalability for Big Data

**Linear vs. Parallel Processing**

- **Linear Processing:**
  - **Sequential Nature:** Executing a set of instructions sequentially.
  - **Error Impact:** If an error occurs, the entire sequence restarts.
  - **Suitability:** Best for minor tasks, inefficient for complex tasks like Big Data.

- **Parallel Processing:**
  - **Parallel Execution:** Instructions distributed to multiple nodes, executed simultaneously.
  - **Error Handling:** Errors can be fixed and executed locally, independent of others.
  - **Benefits for Big Data:** Reduced processing times, lower memory and processing requirements, flexibility in adding/removing nodes.

**Data Scaling and Scaling Strategies**

Data Scaling is a technique for managing, storing, and processing data overflow.

- **Scaling Up (Vertically):** Increasing capacity of a single node.
- **Scaling Out (Horizontally):** Adding nodes horizontally, forming a *computing cluster*. This scaling method handles growing data volumes more effectively and reduces infrastructure costs.


**Embarrassingly Parallel Calculations**

In this form of calculation, a computing cluster is formed and workloads are easily divided and run independently. If any one process fails, it has no impact on the others and can simply be rerun.

![Embarrassingly Parallel](assets/images/embarrassingly-parallel.drawio.svg)

**Example:** Changing date format in a large dataset split into smaller chunks across nodes.

Sometimes, sorting a large data set adds significant complexity to the process. Now, the multiple computations must coordinate with one another because each process needs to be aware of the state of its peer processes in order to complete the calculation. This requires sending messages across a network to each other or writing them to a file system that is accessible to all processes on the cluster. The level of complexity increases significantly, because you are basically asking a cluster of computers to behave as a single computer.
Most calculations done in enterprise environments are like this and are considered *"embarrassingly parallel with some not so easy"* calculations.

**Design Principles in Hadoop Ecosystem**

In the Hadoop ecosystem, the concept of *"bringing compute to the data"* is a central idea in the design of the cluster. The cluster is designed in a way that computations on certain pieces, or partitions, of the data will take place right at the location of the data when possible. The resulting output will also be written to the same node (Data Locality).

![Embarrassingly Parallel with Data Locality](assets/images/embarrassingly-parallel-data-locality.drawio.svg)


**Fault Tolerance in Parallel Computing**

Fault tolerance is system's ability to continue operating without interruption despite component failures.

In Hadoop FileSystem (HDFS):
- Copies of data partitions stored on multiple nodes.
- Recovery from a node failure involves copying data from other nodes.

![Fault Tolerance in Parallel Computing](assets/images/embarrassingly-parallel-fault-tolerance.drawio.svg)


### 1.3. Big Data Tools and Ecosystem

**Categories of Big Data Tools**

- Data Technologies
- Analytics and Visualization
- Business Intelligence
- Cloud Service Providers
- NoSQL Databases
- Programming Tools

**Data Technologies**

- **Role:** Designed for analyzing, processing, and extracting meaningful information from Big Data.
- **Capabilities:** Handle structured and unstructured data, offer high-performance parallel processing.
- **Examples:** Apache Hadoop, Apache HDFS, Apache Spark. Vendors: Cloudera, Databricks.

**Analytics and Visualization**

- **Role:** Examine large data to find patterns; visualize trends via graphs, charts, maps.
- **Examples:** Tableau, Palantir, SAS, Pentaho, Teradata.

**Business Intelligence (BI)**

- **Role:** Transform raw data into actionable information for business analysis.
- **Techniques:** Include probability, statistics, and flowcharts.
- **Examples:** Cognos, Oracle, PowerBI, Business Objects, Hyperion.

**Cloud Service Providers**

- **Role:** Offer infrastructure, support, shared computing resources for storing, processing, and analyzing data.
- **Examples:** AWS, IBM, GCP, Oracle.
- **Models:** Provide "software as a service" (SaaS) for data aggregation, processing, and visualization.

**NoSQL Databases**

- **Role:** Tailor-made for Big Data processing; store and process vast amounts of data at scale.
- **Structure:** Store data in documents (not relational tables).
- **Types:** Document databases, key-value stores, wide-column databases, graph databases.
- **Examples:** MongoDB, CouchDB, Cassandra, Redis.

**Programming Tools**

- **Role:** Perform large-scale analytical tasks, operationalize Big Data.
- **Functions:** Cover data collection, cleaning, exploration, modeling, visualization.
- **Examples:** R, Python, SQL, Scala, Julia.


<br>
<br>

****************