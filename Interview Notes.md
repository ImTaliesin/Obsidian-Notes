```I'm Brennan, everybody in my life usually calls me by nickname Talie. I've always been a tech geek since I was a kid, always tinkering with computers. Not too long ago I graduated from Fresno State University with an English degree focused on Education. After working in education after covid, I realized it wasn't for me and I should've stuck with my passion for tech. The last two years I've been working almost every day to build my foundation in tech, got a job at a hotel near me working as IT. I took about a dozen courses and bootcamps online, then started doing freelance programming work on the side, made websites and programs for myself, friends, and clients. Started specializing in data engineering a little over a year ago. I was able to find a mentor who let me intern at his company and work on projects under his guidance, got some hands on experience with Azure Synapse, Data Factory, and Databricks. Fast forward to this december, I recently got my Azure Data Engineering certificate from microsoft, and I believe I'm ready for a full-time position as a Data Engineer.
```
# Spark

## Specify Schema
	name_df.printSchema()
	name_df.describe.show()

	from pyspark.sql.types import StructType, StructField, IntegerType, 
	StringType, DoubleType

	name_shema = StructType(fields=[
				StructField("columnameID", IntegerType(), False),
				StructField("columnameLocation", StringType(), True),
				StructField("columnameCountry", StringType(), True),
				StructField("columnameLat", DoubleType() True)
				])

	name_df = spark.read \
		.option("header", True) \
		.schema(name_schema) \
		.csv("dbfs:/mnt/datalakename/raw/name.csv")
## CSV Read unspecified schema
	name_df = spark.read \
		.option("header", True) \
		.option("inferSchema", True) \
		.csv("dbfs:/mnt/datalakename/raw/name.csv")

## WithColumn() - adds a column & currentTimestamp
	from pyspark.sql.functions import current_timestamp
	updated_df = name_df.withColumn("ingestion_date", current_timestamp())

## lit()  - adds a column with one value for every row
	updated_df = name_df.withColumn("env", lit("Production"))

## Write to DB as Parquet
	df.write.mode("overwrite").parquet("/mnt/datalake/processed/name")

## Delta Tables
* Used in databricks, holds data and metadata
* Data is in parquet, metadata/change log stored in JSON
* ACID Compliant
	* - **Atomicity:**
		* A transaction is treated as a single unit, either completing entirely or failing completely.
		
	* **Consistency:**
		* Every transaction brings the database from one valid state to another, upholding defined rules and constraints.
	 * **Isolation:**
		 * Multiple transactions occurring concurrently do not interfere with each other, ensuring each transaction appears to execute independently.
	* ***Durability:**
		* Once a transaction is committed, the changes are permanently stored and will not be lost even if a system crash happens.
* Optimization
		* z-ordering, organizing data based on columns
		* increased read performance
	* Vacuum command
		* Soft delete data
	* Partitioning
		* on date, etc
# SQL

SELECT
FROM
WHERE
	Filters individual rows BEFORE they are grouped
	Used with raw columns, not aggregates
GROUP BY
	Used with aggregate functions (COUNT, SUM, AVG, etc.)
	Changes the actual data structure by condensing multiple rows into one
ORDRER BY
	Sorts the final results in ascending (ASC) or descending (DESC) order
	Doesn't change the data structure, just the display order
HAVING
	Filters grouped results AFTER GROUP BY
	Used with aggregate functions (COUNT, SUM, AVG, etc.)

## Full Queries

### Sub-Query + Extract date (Twitter interview)
WITH user_tweet_counts AS (
  SELECT 
    user_id,
    COUNT(*) as tweet_bucket
  FROM tweets
  WHERE EXTRACT(YEAR FROM tweet_date) = 2022
  GROUP BY user_id
)
SELECT 
  tweet_bucket,
  COUNT(*) as users_num
FROM user_tweet_counts
GROUP BY tweet_bucket
ORDER BY tweet_bucket;

| tweet_bucket | users_num |
| ------------ | --------- |
| 1            | 2         |
| 2            | 1         |

### Subquery (LinkedIn interview)
with subquery as (
	select candidate_id, count(skill) from candidates
	where skill in ('Python', 'Tableau', 'PostgreSQL')
group by candidate_id
) select candidate_id from subquery
	where count = 3
group by candidate_id

### Double Subquery (Mobile_views, Laptop_views)
with laptop_views as (
	select 
	count(device_type) as laptop_views
from viewership
	where device_type = 'laptop'
),
mobile_views as (
	SELECT
	count(device_type) as mobile_views
from viewership
	where device_type in ('tablet', 'phone')
)
select laptop_views, mobile_views
from laptop_views
cross JOIN mobile_views

### Days between posts (Facebook)
SELECT 
	user_id, 
    MAX(DATE(post_date))- MIN(DATE(post_date)) AS days_between
FROM
	posts
WHERE
  extract(year from post_date) = 2021
GROUP BY 
	user_id
HAVING
	COUNT(post_id)>1

### Teams power users (Microsoft)
SELECT sender_id, count(*) as message_count FROM messages 
	where EXTRACT(MONTH from sent_date) = 8 and extract(year from sent_date) = 2022
group by sender_id
order by message_count desc
LIMIT 2



### RANK() OVER (PARTITION BY column ORDER BY COUNT(*) DESC) AS rnk

WITH actor_category_counts AS (
  SELECT 
    fc.category_id,
    fa.actor_id,
    COUNT(*) AS movies,
    RANK() OVER (PARTITION BY fc.category_id ORDER BY COUNT(*) DESC) AS rnk
  FROM film_category fc
  JOIN film_actor fa ON fc.film_id = fa.film_id
  GROUP BY fc.category_id, fa.actor_id
)
SELECT 
  category_id,
  actor_id,
  movies AS num_movies
FROM actor_category_counts
WHERE rnk = 1
ORDER BY category_id;
## SCD Types
### Type 0
- No tracking of historical changes
- Original data never changes
- Example: Birth date, Social Security number
### Type 1
- Current value overwrites old value
- No history maintained
- Simple but loses historical data
- Example: Address correction due to typo
### Type 2
- Maintains history with new records
- Uses start/end dates or flags
- Additional columns:
  - Start_Date
  - End_Date
  - Current_Flag
- Example: Customer address changes
### Type 3
- Keeps current and previous value only
- Uses separate columns for old/new
- Limited history (one change)
- Example: Previous_Price, Current_Price

### Type 4
- Historical records in separate history table
- Current data in main table
- Requires joins for full history
- Example: Customer_Current, Customer_History

### Type 6
- Combines Types 1, 2, and 3
- Most flexible but complex
- Tracks current, previous, and full history
- Example: Product pricing with all changes
### Key Interview Points
- Performance implications
- Storage requirements
- Business use cases
- Implementation complexity
- Data quality considerations

# Python

## Decorators
Think of decorators as wrappers that add a little bit of functionality to other functions/classes

```python
def show_call(obj):
    """
    Decorator that prints obj name and arguments each time obj is called.
    """
    def show_call_wrapper(*args, **kwds):
        print(obj.__name__, args, kwds)
        return obj(*args, **kwds)
    return show_call_wrapper

@show_call # function decorator
def add(x, y):
    return x + y

# is equivalent to
add = show_call(add)

>>> add(13, 29)
add (13, 29) {}
42

@show_call # class decorator
class C:
    def __init__(self, a=None):
        pass

# is equivalent to
C = show_call(C)

>>> C(a=42)
C () {'a': 42}
```

## HTTP Requests

### Extract files from https
```python
import requests
import zipfile
import io
import os
import pandas as pd

download_uris = [
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2018_Q4.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2019_Q1.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2019_Q2.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2019_Q3.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2019_Q4.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2020_Q1.zip",
    "https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2020_Q1.zip",
]


def main():
    # Create a directory to store the extracted files
    output_dir = "extracted_data"
    os.makedirs(output_dir, exist_ok=True)

    # Process each zip file
    for uri in download_uris:
        try:
            print(f"Downloading {uri}")
            response = requests.get(uri)
            if response.status_code == 200:
                print("Download successful")
                with zipfile.ZipFile(io.BytesIO(response.content)) as zip_ref:
                    # Print contents of the zip file
                    zip_ref.printdir()

                    print("\nExtracting files...")
                    zip_ref.extractall(output_dir)
                    print("Done extracting")
            else:
                print(f"Failed to download {uri}. Status code: {response.status_code}")
        except Exception as e:
            print(f"Error processing {uri}: {str(e)}")


if __name__ == "__main__":
    main()

```

``