## Question 1: 
What is count of records for the 2024 Yellow Taxi Data?
```
-- Create external table in BigQuery
CREATE OR REPLACE EXTERNAL TABLE `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://dezoomcamp_hw3_2025_447406_a8/yellow_tripdata_2024-*.parquet']
);

-- Create materialized non-partitioned table in Bigquery
CREATE OR REPLACE TABLE `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`
AS SELECT * FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3`;

-- Create materialized partitioned table in Bigquery
CREATE OR REPLACE TABLE `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_partitioned`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS (
  SELECT * FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3`
);

-- count of records for the 2024 Yellow Taxi Data
SELECT count(*) FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3`;
```
Answer = 20,332,093

## Question 2:
Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?
```
SELECT COUNT(DISTINCT(`PULocationID`)) FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3`;
SELECT COUNT(DISTINCT(`PULocationID`)) FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`;
```
Answer = 0 MB for the External Table and 155.12 MB for the Materialized Table

## Question 3:
Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table. Why are the estimated number of Bytes different?
```
SELECT `PULocationID` FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`;
SELECT `PULocationID`, `DOLocationID` FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`;
```
Answer = BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.

## Question 4:
How many records have a fare_amount of 0?
```
SELECT COUNT(1) as zero_fare_amount_cnt  FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`
where fare_amount = 0;
```
Answer = 8,333

## Question 5:
What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)

Answer = Partition by tpep_dropoff_datetime and Cluster on VendorID

## Question 6:
Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 03/01/2024 and 03/15/2024 (inclusive)

Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 4 and note the estimated bytes processed. What are these values?

Choose the answer which most closely matches.
```
SELECT DISTINCT VendorID FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_nonpartitioned`
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15'
;

SELECT DISTINCT VendorID FROM `fourth-waters-447406-a8.zoomcamp.yellow_tripdata_hw3_partitioned`
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15'
;
```
Answer = 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table

## Question 7:
Where is the data stored in the External Table you created?

Answer = GCP Bucket

## Question 8:
It is best practice in Big Query to always cluster your data:

Answer = False