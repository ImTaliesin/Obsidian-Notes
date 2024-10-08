[Docs](https://docs.delta.io/latest/delta-apidoc.html) [Activity](https://microsoftlearning.github.io/dp-203-azure-data-engineer/Instructions/Labs/07-Use-delta-lake.html)
Delta Lake is an **open-source storage layer** that adds relational [[database]] semantics to Spark-based data lake processing. Delta Lake is supported in Azure Synapse Analytics Spark pools for PySpark, Scala, and .NET code. [[SparkSQL]] 

notes include how to:
- Describe core features and capabilities of Delta Lake.
- Create and use Delta Lake tables in a Synapse Analytics Spark pool.
- Create Spark catalog tables for Delta Lake data.
- Use Delta Lake tables for streaming data.
- Query Delta Lake tables from a Synapse Analytics SQL pool.
## Merge INTO
Merges a set of updates, insertions, and deletions based on a source table into a target Delta table.

Setup:
```sql
--Creating the target table
create table main.default.target_table(id INT, name STRING, age INT);
insert into main.default.target_table vales (1, 'John', 30);
insert into main.default.target_table values (3,'Megan', 22);

select * from main.default.target_table;

--creating the source table
create table main.default.source_table(id INT, name STRING, age INT);
insert into main.default.source_table values (1 'John', 20);
insert into main.default.source_table values (2, 'Anika' 19);

select * from main.default.source_table;
```
EXAMPLE:
```sql
merge into main.default.target_table as t
	using main.default.source_table as s
	on t.id = s.id
	when matched then update set *
	when not matched then insert *
--- For all rows where a match is found (based on the ON condition),Update ALL columns in the target table and set them to the values from the corresponding columns in the source table. if the id's dont match then insert the data from the source to the target

merge into main.default.target_table as t
	using main.default.source_table as s
	on t.id = s.id
	when matched then update set t.name = s.name, t.age = s.age
	when not matched then insert (t.id, t.name, t.age) values (s.id, s.name s.age)
--
```

Usage:
```sql
MERGE INTO target_table_name [AS target_alias]
USING (source_table_reference) [AS source_alias]
ON merge_condition
WHEN MATCHED [AND <matched_condition>]
    THEN <matched_action>
WHEN NOT MATCHED [BY TARGET] [AND <not_matched_condition>]
    THEN <not_matched_action>
WHEN NOT MATCHED BY SOURCE [AND <not_matched_by_source_condition>]
    THEN <not_matched_by_source_action>;

-- Where matched_action can be:
UPDATE SET column1 = value1 [, column2 = value2 ...]
DELETE

-- And not_matched_action can be:
INSERT (column1 [, column2 ...]) VALUES (value1 [, value2 ...])
```

### Explanation:

1. `MERGE INTO target_table_name`: Specifies the table you want to merge changes into.
2. `USING (source_table_reference)`: Specifies the source of data for the merge operation. This can be a table, view, or even a subquery.
3. `ON merge_condition`: Defines how to match rows between the target and source.
4. `WHEN MATCHED`: Specifies what to do when a row in the target table matches a row in the source.
    - You can UPDATE or DELETE matched rows.
5. `WHEN NOT MATCHED [BY TARGET]`: Specifies what to do when a row in the source doesn't match any row in the target.
    - Typically used to INSERT new rows.
6. `WHEN NOT MATCHED BY SOURCE`: Specifies what to do with rows in the target that don't match any in the source.
    - Often used to DELETE or UPDATE rows that no longer exist in the source.
7. Each WHEN clause can have an additional condition for more granular control.

```sql
MERGE INTO Customers AS target
USING (SELECT * FROM NewCustomers) AS source
ON (target.CustomerID = source.CustomerID)
WHEN MATCHED AND source.LastPurchase > target.LastPurchase
    THEN UPDATE SET 
        target.LastPurchase = source.LastPurchase,
        target.TotalPurchases = target.TotalPurchases + source.PurchaseAmount
WHEN NOT MATCHED BY TARGET 
    THEN INSERT (CustomerID, Name, LastPurchase, TotalPurchases)
         VALUES (source.CustomerID, source.Name, source.LastPurchase, source.PurchaseAmount)
WHEN NOT MATCHED BY SOURCE 
    THEN DELETE;
```

This MERGE statement:
- Updates existing customers if there's a newer purchase
- Inserts new customers
- Deletes customers that no longer exist in the source

### Key Points:
- MERGE is an atomic operation – it's all or nothing.
- It's more efficient than separate INSERT, UPDATE, and DELETE operations.
- Not all database systems support all features of MERGE.
- The order of WHEN clauses can matter for performance.
## When to use Delta Lake:

1. When you need ACID transactions in your data lake: Delta Lake brings transactional integrity to your data lake operations. This is crucial when you need to ensure data consistency and reliability, especially in scenarios involving concurrent reads and writes.
2. For data versioning and time travel capabilities: If your use case requires the ability to access and query previous versions of your data, Delta Lake's time travel feature is invaluable. This is particularly useful for auditing, reproducing past results, or rolling back to a previous state.
3. When dealing with both batch and streaming data: Delta Lake's ability to handle both batch and streaming data in a unified manner makes it ideal for scenarios where you need to process real-time data alongside historical data.
4. For simplifying ETL processes: Delta Lake's support for CRUD operations allows for more straightforward ETL processes, especially when you need to update or delete specific records in your data lake.
5. When you need to enforce a schema on your data lake: Delta Lake provides schema enforcement, which helps maintain data quality and consistency in your data lake.
6. For optimizing query performance: Delta Lake includes features like data skipping and Z-ordering that can significantly improve query performance on large datasets.
7. When working with Spark in Azure Synapse Analytics: If you're already using Spark in Azure Synapse Analytics, Delta Lake integrates seamlessly and can enhance your data processing capabilities.

### Specific scenarios where Delta Lake shines:

1. IoT data processing: For handling large volumes of streaming sensor data while also allowing for historical analysis.
2. Customer 360 views: When you need to continuously update and query customer data from various sources.
3. Data warehousing: For building a scalable, ACID-compliant data warehouse on top of your data lake.
4. Machine Learning pipelines: To ensure data consistency and versioning in your ML feature stores and model training datasets.
5. Regulatory compliance: When you need to maintain audit trails and have the ability to revert to previous data states for compliance reasons.
6. Real-time analytics: For scenarios requiring both real-time data ingestion and immediate querying capabilities.

When to use Delta Lake:

- You should consider using Delta Lake when you're working with large-scale data in Azure and need the benefits of both data lakes (scalability, support for diverse data types) and traditional databases (ACID transactions, data consistency).
- It's particularly valuable when your data engineering workflows involve frequent updates to data, require strong consistency guarantees, or need to support both batch and streaming operations.
- If you're already using Apache Spark in your Azure environment, integrating Delta Lake can provide significant enhancements to your data management capabilities.
## Delta lakes with streaming data
streaming data can come in real time [docs](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
### Spark Structured Streaming

A typical stream processing solution involves constantly reading a stream of data from a _source_, optionally processing it to select specific fields, aggregate and group values, or otherwise manipulate the data, and writing the results to a _sink_.

Spark includes native support for streaming data through _Spark Structured Streaming_, an API that is based on a boundless dataframe in which streaming data is captured for processing. A Spark Structured Streaming dataframe can read data from many different kinds of streaming source, including network ports, real time message brokering services such as Azure Event Hubs or Kafka, or file system locations.

### Streaming with Delta Lake tables

You can use a Delta Lake table as a source or a sink for Spark Structured Streaming. For example, you could capture a stream of real time data from an IoT device and write the stream directly to a Delta Lake table as a sink - enabling you to query the table to see the latest streamed data. Or, you could read a Delta Table as a streaming source, enabling you to constantly report new data as it is added to the table.

#### Using a Delta Lake table as a streaming source

In the following PySpark example, a Delta Lake table is used to store details of Internet sales orders. A stream is created that reads data from the Delta Lake table folder as new data is appended. 
```
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Load a streaming dataframe from the Delta Table
stream_df = spark.readStream.format("delta") \
    .option("ignoreChanges", "true") \
    .load("/delta/internetorders")

# Now you can process the streaming data in the dataframe
# for example, show it:
stream_df.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()
```
When using a Delta Lake table as a streaming source, only _append_ operations can be included in the stream. Data modifications will cause an error unless you specify the `ignoreChanges` or `ignoreDeletes` option.

After reading the data from the Delta Lake table into a streaming dataframe, you can use the Spark Structured Streaming API to process it. In the example above, the dataframe is simply displayed; but you could use Spark Structured Streaming to aggregate the data over temporal windows (for example to count the number of orders placed every minute) and send the aggregated results to a downstream process for near-real-time visualization.

### Using a Delta Lake table as a streaming sink

In the following PySpark example, a stream of data is read from JSON files in a folder. The JSON data in each file contains the status for an IoT device in the format `{"device":"Dev1","status":"ok"}` New data is added to the stream whenever a file is added to the folder. The input stream is a boundless dataframe, which is then written in delta format to a folder location for a Delta Lake table.
```
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Create a stream that reads JSON data from a folder
inputPath = '/streamingdata/'
jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
])
stream_df = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

# Write the stream to a delta table
table_path = '/delta/devicetable'
checkpoint_path = '/delta/checkpoint'
delta_stream = stream_df.writeStream.format("delta").option("checkpointLocation", checkpoint_path).start(table_path)
```
The `checkpointLocation` option is used to write a checkpoint file that tracks the state of the stream processing. This file enables you to recover from failure at the point where stream processing left off.

After the streaming process has started, you can query the Delta Lake table to which the streaming output is being written to see the latest data. For example, the following code creates a catalog table for the Delta Lake table folder and queries it:
```
%%sql

CREATE TABLE DeviceTable
USING DELTA
LOCATION '/delta/devicetable';

SELECT device, status
FROM DeviceTable;

```
To stop the stream of data being written to the Delta Lake table, you can use the `stop` method of the streaming query:
`delta_stream.stop()`
## Creating a Delta Lake table from a dataframe
One of the easiest ways to create a Delta Lake table is to save a dataframe in the _delta_ format, specifying a path where the data files and related metadata information for the table should be stored.

For example, the following PySpark code loads a dataframe with data from an existing file, and then saves that dataframe to a new folder location in _delta_ format:
 ```
 # Load a file into a dataframe
df = spark.read.load('/data/mydata.csv', format='csv', header=True)

# Save the dataframe as a delta table
delta_table_path = "/delta/mydata"
df.write.format("delta").save(delta_table_path)
```

You can replace an existing Delta Lake table with the contents of a dataframe by using the **overwrite** mode, as shown here:
`new_df.write.format("delta").mode("overwrite").save(delta_table_path)`
You can also add rows from a dataframe to an existing table by using the **append** mode:
`new_rows_df.write.format("delta").mode("append").save(delta_table_path)`

## Create catalog tables
### _External_ vs _managed_ tables

Tables in a Spark catalog, including Delta Lake tables, can be _managed_ or _external_; and it's important to understand the distinction between these kinds of table.

- A _managed_ table is defined without a specified location, and the data files are stored within the storage used by the metastore. Dropping the table not only removes its metadata from the catalog, but also deletes the folder in which its data files are stored.
- An _external_ table is defined for a custom file location, where the data for the table is stored. The metadata for the table is defined in the Spark catalog. Dropping the table deletes the metadata from the catalog, but doesn't affect the data files.

### Creating catalog tables
#### Creating a catalog table from a dataframe

You can create managed tables by writing a dataframe using the `saveAsTable` operation as shown in the following examples: 
```
# Save a dataframe as a managed table
df.write.format("delta").saveAsTable("MyManagedTable")

## specify a path option to save as an external table
df.write.format("delta").option("path", "/mydata").saveAsTable("MyExternalTable")
```
#### Creating a catalog table using SQL

You can also create a catalog table by using the `CREATE TABLE` SQL statement with the `USING DELTA` clause, and an optional `LOCATION` parameter for external tables. You can run the statement using the SparkSQL API, like the following example:
`spark.sql("CREATE TABLE MyExternalTable USING DELTA LOCATION '/mydata'")`
Alternatively you can use the native SQL support in Spark to run the statement:
```
%%sql

CREATE TABLE MyExternalTable
USING DELTA
LOCATION '/mydata'
```

## Making conditional updates

While you can make data modifications in a dataframe and then replace a Delta Lake table by overwriting it, a more common pattern in a database is to insert, update or delete rows in an existing table as discrete transactional operations. To make such modifications to a Delta Lake table, you can use the **DeltaTable** object in the Delta Lake API, which supports _update_, _delete_, and _merge_ operations. For example, you could use the following code to update the **price** column for all rows with a **category** column value of "Accessories":

```
from delta.tables import *
from pyspark.sql.functions import *

# Create a deltaTable object
deltaTable = DeltaTable.forPath(spark, delta_table_path)

# Update the table (reduce price of accessories by 10%)
deltaTable.update(
    condition = "Category == 'Accessories'",
    set = { "Price": "Price * 0.9" })
```

The data modifications are recorded in the transaction log, and new parquet files are created in the table folder as required.
## Benefits of Delta Lakes

- **Relational tables that support querying and data modification**. With Delta Lake, you can store data in tables that support _CRUD_ (create, read, update, and delete) operations. In other words, you can _select_, _insert_, _update_, and _delete_ rows of data in the same way you would in a relational database system.
- **Support for _ACID_ transactions**. Relational databases are designed to support transactional data modifications that provide _atomicity_ (transactions complete as a single unit of work), _consistency_ (transactions leave the database in a consistent state), _isolation_ (in-process transactions can't interfere with one another), and _durability_ (when a transaction completes, the changes it made are persisted). Delta Lake brings this same transactional support to Spark by implementing a transaction log and enforcing serializable isolation for concurrent operations.
- **Data versioning and _time travel_**. Because all transactions are logged in the transaction log, you can track multiple versions of each table row and even use the _time travel_ feature to retrieve a previous version of a row in a query.
- **Support for batch and streaming data**. While most relational databases include tables that store static data, Spark includes native support for streaming data through the Spark Structured Streaming API. Delta Lake tables can be used as both _sinks_ (destinations) and _sources_ for streaming data.
- **Standard formats and interoperability**. The underlying data for Delta Lake tables is stored in Parquet format, which is commonly used in data lake ingestion pipelines. Additionally, you can use the serverless SQL pool in Azure Synapse Analytics to query Delta Lake tables in SQL.

