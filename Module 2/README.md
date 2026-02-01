### Quiz Questions

Complete the quiz shown below. It's a set of 6 multiple-choice questions to test your understanding of workflow orchestration, Kestra, and ETL pipelines.

1) Within the execution for `Yellow` Taxi data for the year `2020` and month `12`: what is the uncompressed file size (i.e. the output file `yellow_tripdata_2020-12.csv` of the `extract` task)?
- 128.3 MiB


2) What is the rendered value of the variable `file` when the inputs `taxi` is set to `green`, `year` is set to `2020`, and `month` is set to `04` during execution?

- green_tripdata_2020-04.csv 

3) How many rows are there for the `Yellow` Taxi data for all CSV files in the year 2020?

- 24,648,499

```sql 
SELECT 
    COUNT(*) 
FROM 
    `zoomcamp.yellow_tripdata` 
WHERE 
EXTRACT (YEAR FROM tpep_pickup_datetime) = 2020
```

4) How many rows are there for the `Green` Taxi data for all CSV files in the year 2020?

- 1,734,051

```sql 
SELECT 
    COUNT (*) 
FROM 
    `zoomcamp.yellow_tripdata` 
WHERE 
    EXTRACT (YEAR FROM lpep_pickup_datetime) = 2020
```

5) How many rows are there for the `Yellow` Taxi data for the March 2021 CSV file?

- 1,925,152

```sql
SELECT
     COUNT(*) 
 FROM
     yellow_tripdata 
 WHERE
     EXTRACT(YEAR FROM tpep_pickup_datetime) = 2021
     AND EXTRACT(MONTH FROM tpep_pickup_datetime) = 03;
```

6) How would you configure the timezone to New York in a Schedule trigger?

- Add a `timezone` property set to `America/New_York` in the `Schedule` trigger configuration

> Solution: The general solution is as follows:
    ```Yaml
    triggers:
    - id: schedule-trigger
        type: io.kestra.core.models.triggers.types.Schedule
        cron: "0 12 * * *"
        timezone: "America/New_York"
    ```