# Module 3 Homework: Data Warehousing & BigQuery
## Data

For this homework we will be using the Yellow Taxi Trip Records for January 2024 - June 2024 (not the entire year of data).

Parquet Files are available from the New York City Taxi Data found here:

https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

## Loading the data

I use the following script to load the data into your GCS bucket:

- Jupyter notebook with DLT: [DLT_upload_to_GCP.ipynb](./DLT_upload_to_GCP.ipynb)


## BigQuery Setup

Create an external table using the Yellow Taxi Trip Records. 

```sql

CREATE SCHEMA IF NOT EXISTS `kestra-sandbox-485613.rides_dataset`;

CREATE OR REPLACE EXTERNAL TABLE `kestra-sandbox-485613.rides_dataset.external_yellow_tripdata`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://kestra-zoomcamp-joseph-demo/rides_dataset/rides/yellow_tripdata_2024_*.parquet']
);
```

Create a (regular/materialized) table in BQ using the Yellow Taxi Trip Records (do not partition or cluster this table). 

```sql
CREATE OR REPLACE TABLE `kestra-sandbox-485613.rides_dataset.yellow_taxi`
AS
SELECT *
FROM `kestra-sandbox-485613.rides_dataset.external_yellow_tripdata`; 
```

## Question 1. Counting records

What is count of records for the 2024 Yellow Taxi Data?
- 65,623
- 840,402
- 20,332,093 ✅
- 85,431,289
```sql
SELECT COUNT(*) FROM `rides_dataset.yellow_taxi`;
```

## Question 2. Data read estimation

Write a query to count the distinct number of s for the entire dataset on both the tables.
 PULocationID
What is the **estimated amount** of data that will be read when this query is executed on the External Table and the Table?
```sql
-- Query 1: External Table
SELECT COUNT(DISTINCT pu_location_id)
FROM `kestra-sandbox-485613.rides_dataset.external_yellow_tripdata`;

-- Query 2: Materialized Table
SELECT COUNT(DISTINCT pu_location_id)
FROM `kestra-sandbox-485613.rides_dataset.yellow_taxi`;
```

- 0 MB for the External Table and 155.12 MB for the Materialized Table ✅


## Question 3. Understanding columnar storage

Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table.

Why are the estimated number of Bytes different?
- BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires 
reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed. ✅
```sql
SELECT pu_location_id
FROM `kestra-sandbox-485613.rides_dataset.yellow_taxi`;

SELECT pu_location_id,do_location_id
FROM `kestra-sandbox-485613.rides_dataset.yellow_taxi`;
```

## Question 4. Counting zero fare trips

How many records have a fare_amount of 0?

 8,333 ✅
```sql
SELECT COUNT(*) 
FROM `kestra-sandbox-485613.rides_dataset.external_yellow_tripdata`
WHERE fare_amount = 0;
```
## Question 5. Partitioning and clustering

What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)

- Partition by tpep_dropoff_datetime and Cluster on VendorID ✅
```sql
CREATE OR REPLACE TABLE `kestra-sandbox-485613.rides_dataset.yellow_tripdata_optimized`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY vendor_id
AS
SELECT * FROM `kestra-sandbox-485613.rides_dataset.external_yellow_tripdata`:
```

## Question 6. Partition benefits

Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime
2024-03-01 and 2024-03-15 (inclusive)


Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 5 and note the estimated bytes processed. What are these values? 


Choose the answer which most closely matches.
 

```sql

SELECT DISTINCT vendor_id
FROM `kestra-sandbox-485613.rides_dataset.yellow_taxi` 
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15';

SELECT DISTINCT vendor_id
FROM `kestra-sandbox-485613.rides_dataset.yellow_tripdata_optimized` 
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15';

```
- 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table ✅



## Question 7. External table storage

Where is the data stored in the External Table you created?

- GCP Bucket ✅

## Question 8. Clustering best practices

It is best practice in Big Query to always cluster your data:
- True
- False ✅


## Question 9. Understanding table scans

No Points: Write a `SELECT count(*)` query FROM the materialized table you created. How many bytes does it estimate will be read? Why?
```sql
SELECT count(*)
FROM `kestra-sandbox-485613.rides_dataset.yellow_taxi
```

## Submitting the solutions

Form for submitting: https://courses.datatalks.club/de-zoomcamp-2026/homework/hw3
