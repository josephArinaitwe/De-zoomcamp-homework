# Module 6 Homework

```bash
wget https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2025-11.parquet
```


## Question 1: Install Spark and PySpark

- Install Spark
- Run PySpark
- Create a local spark session
- Execute spark.version.

What's the output?
```
4.1.1
```

> [!NOTE]
> To install PySpark follow this [guide](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/06-batch/setup/)


## Question 2: Yellow November 2025

Read the November 2025 Yellow into a Spark Dataframe.

Repartition the Dataframe to 4 partitions and save it to parquet.

What is the average size of the Parquet (ending with .parquet extension) Files that were created (in MB)? Select the answer which most closely matches.

- 6MB
- 25MB ✅
- 75MB
- 100MB


## Question 3: Count records

How many taxi trips were there on the 15th of November?

Consider only trips that started on the 15th of November.

- 62,610
- 102,340
- 162,604 ✅
- 225,768
```sql
from pyspark.sql import functions as F

count_nov_15 = df \
    .withColumn('pickup_date', F.to_date(df.tpep_pickup_datetime)) \
    .filter(F.col('pickup_date') == '2025-11-15') \
    .count()

print(f"Number of trips on November 15th: {count_nov_15:,}")
```

## Question 4: Longest trip

What is the length of the longest trip in the dataset in hours?

- 22.7
- 58.2
- 90.6 ✅
- 134.5
  ```sql
  df_with_duration = df.withColumn('duration_hrs', 
    (F.unix_timestamp(df.tpep_dropoff_datetime) - F.unix_timestamp(df.tpep_pickup_datetime)) / 3600
)
max_duration = df_with_duration.select(F.max('duration_hrs')).collect()[0][0]

print(f"The longest trip was {max_duration:.2f} hours")
  ```

## Question 5: User Interface

Spark's User Interface which shows the application's dashboard runs on which local port?

- 80
- 443
- 4040 ✅
- 8080



## Question 6: Least frequent pickup location zone

Load the zone lookup data into a temp view in Spark:

```bash
wget https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv
```

Using the zone lookup data and the Yellow November 2025 data, what is the name of the LEAST frequent pickup location Zone?

- Governor's Island/Ellis Island/Liberty Island
- Arden Heights
- Rikers Island
- Jamaica Bay



