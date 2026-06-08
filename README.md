# CST8921 – Cloud Industry Trends

## Lab 4 – Azure Databricks and Analyzing Files with Databricks

**Student Name:** Sara Mirzaei  
**Course:** CST8921 – Cloud Industry Trends  
**Lab:** Lab 4 – Azure Databricks and Analyzing Files with Databricks   
**Date:** 06.08.2026  

---

# Objective

The objective of this lab was to explore raw data stored in Azure Data Lake Storage, analyze the data using Azure Databricks, perform data transformation operations using SQL and Spark, and create a refined dataset suitable for analytical reporting.

---

Storage Account Name:

```text
lab4saramirzaei
```

Containers created:

```text
raw
refined
```

---

# Step 1 – Upload Data to Azure Data Lake Storage

A Storage Account with Hierarchical Namespace enabled was created and configured as Azure Data Lake Storage Gen2.

The following Parquet files were uploaded:

```text
raw/customers/customers.parquet
raw/orders/orders.parquet
raw/order_events/order_events.parquet
```

### Screenshot 1 – Storage Account Containers

<img width="975" height="1159" alt="image" src="https://github.com/user-attachments/assets/8e523377-00d6-496c-98a8-7b409874bef0" />

---

# Step 2 – Azure Databricks Configuration

An Azure Databricks workspace was created and a notebook was configured using Serverless Compute.

The uploaded Parquet files were imported into Databricks and converted into Unity Catalog tables:

* customers
* orders
* order_events

---

# Step 3 – Data Exploration

The schema of each dataset was examined using SQL DESCRIBE commands.

## Customers Table Schema

| Column Name | Data Type |
| ----------- | --------- |
| customer_id | string    |
| country     | string    |
| signup_date | date      |

### Screenshot 4 – Customers Schema

<img width="975" height="579" alt="customers" src="https://github.com/user-attachments/assets/c0772454-17af-4763-96e5-f49d52a53b09" />

---

## Orders Table Schema

| Column Name  | Data Type |
| ------------ | --------- |
| order_id     | string    |
| customer_id  | string    |
| order_date   | timestamp |
| order_amount | double    |
| order_status | string    |

### Screenshot 5 – Orders Schema

<img width="975" height="579" alt="orders" src="https://github.com/user-attachments/assets/6f066b42-a0c4-4082-b12e-fca10aadb42e" />

---

## Order Events Table Schema

| Column Name | Data Type |
| ----------- | --------- |
| event_id    | string    |
| order_id    | string    |
| event_time  | timestamp |
| event_type  | string    |

### Screenshot 6 – Order Events Schema

<img width="975" height="579" alt="Order Events" src="https://github.com/user-attachments/assets/1176cbcf-081a-49f4-992c-d00afa6d3a14" />

---

# Step 5 – Data Transformation

The order_events dataset was selected for transformation.

The following transformations were applied:

1. Duplicate records were removed using DISTINCT.
2. The event_time field was validated as a timestamp.
3. A Year column was derived from event_time.
4. A Month column was derived from event_time.
5. The transformed data was stored in a new table called refined_events.

Transformation SQL:

```sql
CREATE OR REPLACE TABLE lab4_databricks.default.refined_events AS
SELECT DISTINCT
    event_id,
    order_id,
    CAST(event_time AS TIMESTAMP) AS event_time,
    event_type,
    YEAR(CAST(event_time AS TIMESTAMP)) AS Year,
    MONTH(CAST(event_time AS TIMESTAMP)) AS Month
FROM lab4_databricks.default.order_events;
```

### Screenshot 10 – Refined Events Table

<img width="975" height="579" alt="Redefined Order Event" src="https://github.com/user-attachments/assets/da579f60-18eb-4e4b-9139-d7da89226ad1" />

---

# Step 6 – Validation of Duplicate Removal

Record counts were compared before and after transformation to verify duplicate removal.

Validation Query:

```sql
SELECT
    (SELECT COUNT(*) FROM lab4_databricks.default.order_events) AS original_count,
    (SELECT COUNT(*) FROM lab4_databricks.default.refined_events) AS refined_count;
```

### Screenshot 11 – Record Count Validation

<img width="975" height="579" alt="image" src="https://github.com/user-attachments/assets/4066bd76-378e-471d-8903-dead7de1782c" />

---

# Step 7 – Data Analysis

The transformed dataset was analyzed by grouping events by year.

Analysis Query:

```sql
SELECT Year, COUNT(*) AS total_events
FROM lab4_databricks.default.refined_events
GROUP BY Year
ORDER BY Year;
```

The query successfully summarized the total number of events occurring in each year and demonstrated how transformed data can be used for reporting and analytics.

### Screenshot 12 – Event Analysis by Year

<img width="975" height="579" alt="image" src="https://github.com/user-attachments/assets/b05f5a1e-467d-4b78-a111-dbf21901188c" />

---

# Findings and Analysis

During this lab, Azure Databricks was used to explore, transform, and analyze Parquet datasets. Three datasets containing customer, order, and event information were successfully loaded into Databricks and converted into managed tables.

The schema inspection process confirmed the data structure and data types for each dataset. Data transformation techniques were applied to remove duplicate records and create derived columns for Year and Month. These transformations improved the usability of the dataset for analytical workloads.

The final analysis demonstrated how Databricks can efficiently process large datasets and generate meaningful insights using SQL-based analytics.

---

# Challenges Encountered

During the lab, direct integration between Azure Databricks Serverless and Azure Data Lake Storage Gen2 required additional configuration and permissions. As a result, the uploaded Parquet files were imported into Unity Catalog storage and converted into managed Databricks tables before analysis was performed.

Despite the configuration challenges, all required exploration, transformation, and analytical objectives were successfully completed.

---

# Conclusion

This lab demonstrated how Azure Databricks can be used to explore raw data, perform transformations, and generate analytical insights from Parquet datasets. The process included schema discovery, duplicate removal, timestamp processing, creation of derived attributes, and aggregation-based reporting.

The lab provided practical experience with Databricks, Spark-based data processing, and cloud analytics workflows commonly used in modern data engineering environments.

---
