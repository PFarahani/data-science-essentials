# Data Cleaning: A Brief Guide <!-- omit from toc -->


## Table of Contents <!-- omit from toc -->
- [1. Why Clean Your Data?](#1-why-clean-your-data)
- [2. What This Guide Is For](#2-what-this-guide-is-for)
- [3. Data Cleaning: A 3-Step Process](#3-data-cleaning-a-3-step-process)
  - [3.1. Step 1: Find the Dirt](#31-step-1-find-the-dirt)
  - [3.2. Step 2: Scrub the Dirt](#32-step-2-scrub-the-dirt)
    - [3.2.1. Missing Data](#321-missing-data)
    - [3.2.2. Outliers](#322-outliers)
    - [3.2.3. Contaminated Data](#323-contaminated-data)
    - [3.2.4. Inconsistent Data](#324-inconsistent-data)
    - [3.2.5. Invalid Data](#325-invalid-data)
    - [3.2.6. Duplicate Data](#326-duplicate-data)
    - [3.2.7. Data Type Issues](#327-data-type-issues)
    - [3.2.8. Structural Errors](#328-structural-errors)
  - [3.3. Step 3: Rinse and Repeat](#33-step-3-rinse-and-repeat)
- [4. Automate Your Data Cleaning](#4-automate-your-data-cleaning)


---

### 1. Why Clean Your Data?
Cleaning your data is essential for various reasons:

- **Prevents Wasting Time**: Avoids spending time on faulty analysis.
- **Accurate Conclusions**: Prevents wrong conclusions that can damage your credibility.
- **Efficient Analysis**: Properly cleaned data speeds up computation in advanced algorithms.

---

### 2. What This Guide Is For
This guide will take you through the process of cleaning data, diving into the practical aspects and little details that make the big picture shine brighter.

---

### 3. Data Cleaning: A 3-Step Process

#### 3.1. Step 1: Find the Dirt
Start by determining what is wrong with your data. Look for:

- **Missing Data**: Rows with empty values, entire columns with no data.
- **Outliers**: Data points that are significantly different from others.
- **Contaminated Data**: Data that doesn't belong in the dataset.
- **Inconsistent Data**: Data that is inconsistent in format or values.
- **Invalid Data**: Data that doesn't make logical sense.
- **Duplicate Data**: Repeated data points.
- **Data Type Issues**: Problems specific to data types.
- **Structural Errors**: Errors arising during measurement or data transfer.

Wear your detective hat and jot down everything interesting, surprising, or weird.

#### 3.2. Step 2: Scrub the Dirt
Depending on the type of data dirt you’re facing, you’ll need different cleaning techniques. This is the most intensive step.

##### 3.2.1. Missing Data
Handle missing data by:

1. **Dropping** rows/columns with missing data.
2. **Recoding** missing data into a different format.
3. **Filling in** missing values with best guesses.

##### 3.2.2. Outliers
Manage outliers by:

1. **Removing** outliers from the analysis.
2. **Segmenting** data so outliers are in a separate group.
3. **Using different statistical methods** for analysis with outliers.

##### 3.2.3. Contaminated Data
Remove contaminated data by identifying and purging it, often requiring domain expertise.

##### 3.2.4. Inconsistent Data
Standardize inconsistent data by visualizing and correcting inconsistencies in the dataset.

##### 3.2.5. Invalid Data
Correct invalid data by amending the functions and transformations that caused the data to be invalid, or remove the data if correction is not possible.

##### 3.2.6. Duplicate Data
Eliminate duplicates by:

1. **Finding and deleting** duplicate records.
2. **Pairwise matching** records and taking the most relevant one.
3. **Combining records** into entities via clustering.

##### 3.2.7. Data Type Issues
Address data type issues by:

1. **Cleaning strings**: Standardizing casing, removing whitespace, correcting typos, etc.
2. **Cleaning date and time**: Ensuring proper DateTime object or Unix timestamp, handling time zones.

##### 3.2.8. Structural Errors
Prevent structural errors by reviewing and fixing your ETL pipeline and data collection processes.

#### 3.3. Step 3: Rinse and Repeat
Repeat the cleaning process to:

1. Catch hidden issues.
2. Discover new issues.
3. Learn more about your data.

---

### 4. Automate Your Data Cleaning
To avoid losing time while ensuring thorough data cleaning, automate repetitive cleaning tasks. Main branches of data cleaning that can be automated include:

1. **Problem discovery**: Using visualization tools to quickly identify issues.
2. **Transforming data**: Running reusable scripts for common cleaning actions.

Keep a list of the main steps by your side to ensure your data provides valuable insights.

---

By following these steps and continuously improving your data cleaning process, you ensure that your data is reliable and your analysis is accurate. Clean data is the foundation for effective decision-making and insightful analysis.
