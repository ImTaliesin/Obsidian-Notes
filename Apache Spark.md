[[PySpark]] [[SparkSQL]]

Apache Spark provides a powerful platform for performing data cleansing and transformation tasks on large volumes of data. By using the Spark _dataframe_ object, you can easily load data from files in a data lake and perform complex modifications. You can then save the transformed data back to the data lake for downstream processing or ingestion into a data warehouse.

Azure Synapse Analytics provides Apache Spark pools that you can use to run Spark workloads to transform data as part of a data ingestion and preparation workload. You can use natively supported notebooks to write and run code on a Spark pool to prepare data for analysis. You can then use other Azure Synapse Analytics capabilities such as SQL pools to work with the transformed data.

## Transform data with Spark
https://microsoftlearning.github.io/dp-203-azure-data-engineer/Instructions/Labs/06-Transform-Data-with-Spark.html

Which method of the Dataframe object is used to save a dataframe as a file?
	write()
Which method is used to split the data across folders when saving a dataframe?
	partitionBy()
What happens if you drop an external table that is based on existing files?
	The table is dropped from the metastore but the files remain unaffected
		The tables are loosely coupled from the files allowing the table to be dropped while the files remain.
### Partition data files
Partitioning is an optimization technique that enables spark to maximize performance across the worker nodes. More performance gains can be achieved when filtering data in queries by eliminating unnecessary disk IO.
#### Partition the output file
To save a dataframe as a partitioned set of files, use the **partitionBy** method when writing the data.

The following example creates a derived **Year** field. Then uses it to partition the data.
```
from pyspark.sql.functions import year, col

# Load source data
df = spark.read.csv('/orders/*.csv', header=True, inferSchema=True)

# Add Year column
dated_df = df.withColumn("Year", year(col("OrderDate")))

# Partition by year
dated_df.write.partitionBy("Year").mode("overwrite").parquet("/data")
```

The folder names generated when partitioning a dataframe include the partitioning column name and value in a _**column=value**_ format, as shown here:

![Diagram representing a partitioned file folder structure.](https://learn.microsoft.com/en-us/training/wwl-data-ai/transform-data-spark-azure-synapse-analytics/media/3-partition-data-files.png)

IMPORTANT:
You can partition the data by multiple columns, which results in a hierarchy of folders for each partitioning key. For example, you could partition the order in the example by year and month, so that the folder hierarchy includes a folder for each year value, which in turn contains a subfolder for each month value.

#### Filter parquet files in a query

When reading data from parquet files into a dataframe, you have the ability to pull data from any folder within the hierarchical folders. This filtering process is done with the use of explicit values and wildcards against the partitioned fields.

In the following example, the following code will pull the sales orders, which were placed in 2020.
```
orders_2020 = spark.read.parquet('/partitioned_data/Year=2020')
display(orders_2020.limit(5))
```
The partitioning columns specified in the file path are omitted in the resulting dataframe. The results produced by the example query would not include a **Year** column - all rows would be from 2020.
## Analyze data with Spark
https://microsoftlearning.github.io/dp-203-azure-data-engineer/Instructions/Labs/05-Analyze-files-with-Spark.html
 key points:

- You can create an empty table by using the `spark.catalog.createTable` method. Tables are metadata structures that store their underlying data in the storage location associated with the catalog. Deleting a table also deletes its underlying data.
- You can save a dataframe as a table by using its `saveAsTable` method.
- You can create an _external_ table by using the `spark.catalog.createExternalTable` method. External tables define metadata in the catalog but get their underlying data from an external storage location; typically a folder in a data lake. Deleting an external table does not delete the underlying data.


### Exploring data with a #dataframe

Natively, Spark uses a data structure called a _resilient distributed dataset_ (RDD); but while you _can_ write code that works directly with RDDs, the most commonly used data structure for working with structured data in Spark is the _dataframe_, which is provided as part of the _Spark SQL_ library. Dataframes in Spark are similar to those in the ubiquitous _Pandas_ Python library, but optimized to work in Spark's distributed processing environment.

### Loading data into a #dataframe
Let's explore a hypothetical example to see how you can use a dataframe to work with data. Suppose you have the following data in a comma-delimited text file named **products.csv** in the primary storage account for an Azure Synapse Analytics workspace:
```
ProductID,ProductName,Category,ListPrice
771,"Mountain-100 Silver, 38",Mountain Bikes,3399.9900
772,"Mountain-100 Silver, 42",Mountain Bikes,3399.9900
773,"Mountain-100 Silver, 44",Mountain Bikes,3399.9900
etc
```

```
%%pyspark
df = spark.read.load('abfss://container@store.dfs.core.windows.net/products.csv',
    format='csv',
    header=True
)
display(df.limit(10))
```

The `%%pyspark` line at the beginning is called a _magic_, and tells Spark that the language used in this cell is PySpark. You can select the language you want to use as a default in the toolbar of the Notebook interface, and then use a magic to override that choice for a specific cell. For example, here's the equivalent Scala code for the products data example:
```
%%spark
val df = spark.read.format("csv").option("header", "true").load("abfss://container@store.dfs.core.windows.net/products.csv")
display(df.limit(10))
```

The magic `%%spark` is used to specify Scala.

Both of these code samples would produce output like this:

|ProductID|ProductName|Category|ListPrice|
|---|---|---|---|
|771|Mountain-100 Silver, 38|Mountain Bikes|3399.9900|
|772|Mountain-100 Silver, 42|Mountain Bikes|3399.9900|
|773|Mountain-100 Silver, 44|Mountain Bikes|3399.9900|
|...|...|...|...|

### Specifying a dataframe schema
In the previous example, the first row of the CSV file contained the column names, and Spark was able to infer the data type of each column from the data it contains. You can also specify an explicit schema for the data, which is useful when the column names aren't included in the data file, like this CSV example:
```
771,"Mountain-100 Silver, 38",Mountain Bikes,3399.9900
772,"Mountain-100 Silver, 42",Mountain Bikes,3399.9900
773,"Mountain-100 Silver, 44",Mountain Bikes,3399.9900
etc
```

The following PySpark example shows how to specify a schema for the dataframe to be loaded from a file named **product-data.csv** in this format:

```
from pyspark.sql.types import *
from pyspark.sql.functions import *

productSchema = StructType([
    StructField("ProductID", IntegerType()),
    StructField("ProductName", StringType()),
    StructField("Category", StringType()),
    StructField("ListPrice", FloatType())
    ])

df = spark.read.load('abfss://container@store.dfs.core.windows.net/product-data.csv',
    format='csv',
    schema=productSchema,
    header=False)
display(df.limit(10))
```


## Visualize data with Spark
One of the most intuitive ways to analyze the result of data queries is to visualize them as charts. 

### Using built-in notebook charts
When you display a dataframe or run a SQL query in a Spark notebook in Azure Synapse Analytics, the results are displayed under the code cell. By default, results are rendered as a table, but you can also change the results view to a chart and use the chart properties to customize how the chart visualizes the data, as shown here:

![A notebook displaying a column chart of product counts by category.](https://learn.microsoft.com/en-us/training/wwl-data-ai/understand-big-data-engineering-with-apache-spark-azure-synapse-analytics/media/notebook-chart.png)

The built-in charting functionality in notebooks is useful when you're working with results of a query that don't include any existing groupings or aggregations, and you want to quickly summarize the data visually. When you want to have more control over how the data is formatted, or to display values that you have already aggregated in a query, you should consider using a graphics package to create your own visualizations.

## Using graphics packages in code

There are many graphics packages that you can use to create data visualizations in code. In particular, Python supports a large selection of packages; most of them built on the base **Matplotlib** library. The output from a graphics library can be rendered in a notebook, making it easy to combine code to ingest and manipulate data with inline data visualizations and markdown cells to provide commentary.

For example, you could use the following PySpark code to aggregate data from the hypothetical products data explored previously in this module, and use Matplotlib to create a chart from the aggregated data.

```
from matplotlib import pyplot as plt

# Get the data as a Pandas dataframe
data = spark.sql("SELECT Category, COUNT(ProductID) AS ProductCount \
                  FROM products \
                  GROUP BY Category \
                  ORDER BY Category").toPandas()

# Clear the plot area
plt.clf()

# Create a Figure
fig = plt.figure(figsize=(12,8))

# Create a bar plot of product counts by category
plt.bar(x=data['Category'], height=data['ProductCount'], color='orange')

# Customize the chart
plt.title('Product Counts by Category')
plt.xlabel('Category')
plt.ylabel('Products')
plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
plt.xticks(rotation=70)

# Show the plot area
plt.show()
```

The Matplotlib library requires data to be in a Pandas dataframe rather than a Spark dataframe, so the **toPandas** method is used to convert it. The code then creates a figure with a specified size and plots a bar chart with some custom property configuration before showing the resulting plot.

The chart produced by the code would look similar to the following image:

![A bar chart showing product counts by category.](https://learn.microsoft.com/en-us/training/wwl-data-ai/understand-big-data-engineering-with-apache-spark-azure-synapse-analytics/media/chart.png)

You can use the Matplotlib library to create many kinds of chart; or if preferred, you can use other libraries such as **Seaborn** to create highly customized charts.

### Filtering and grouping dataframes
You can use the methods of the Dataframe class to filter, sort, group, and otherwise manipulate the data it contains. For example, the following code example uses the **select** method to retrieve the **ProductName** and **ListPrice** columns from the **df** dataframe containing product data in the previous example:

```
pricelist_df = df.select("ProductID", "ListPrice")
```

The results from this code example would look something like this:

|ProductID|ListPrice|
|---|---|
|771|3399.9900|
|772|3399.9900|
|773|3399.9900|
|...|...|

In common with most data manipulation methods, **select** returns a new dataframe object.

*Important*
**Selecting a subset of columns from a dataframe is a common operation, which can also be achieved by using the following shorter syntax:**

`pricelist_df = df["ProductID", "ListPrice"]`

You can "chain" methods together to perform a series of manipulations that results in a transformed dataframe. For example, this example code chains the **select** and **where** methods to create a new dataframe containing the **ProductName** and **ListPrice** columns for products with a category of **Mountain Bikes** or **Road Bikes**:

```
bikes_df = df.select("ProductName", "ListPrice").where((df["Category"]=="Mountain Bikes") | (df["Category"]=="Road Bikes"))
display(bikes_df)
```

The results from this code example would look something like this:

|ProductName|ListPrice|
|---|---|
|Mountain-100 Silver, 38|3399.9900|
|Road-750 Black, 52|539.9900|
|...|...|

To group and aggregate data, you can use the **groupBy** method and aggregate functions. For example, the following PySpark code counts the number of products for each category:

```
counts_df = df.select("ProductID", "Category").groupBy("Category").count()
display(counts_df)
```

The results from this code example would look something like this:

| Category       | count |
| -------------- | ----- |
| Headsets       | 3     |
| Wheels         | 14    |
| Mountain Bikes | 32    |
| ...            | ...   |

### Using SQL expressions in Spark

The Dataframe API is part of a Spark library named Spark SQL, which enables data analysts to use SQL expressions to query and manipulate data.

### Creating database objects in the Spark catalog

The Spark catalog is a metastore for relational data objects such as views and tables. The Spark runtime can use the catalog to seamlessly integrate code written in any Spark-supported language with SQL expressions that may be more natural to some data analysts or developers.

One of the simplest ways to make data in a dataframe available for querying in the Spark catalog is to create a temporary view, as shown in the following code example:

```
df.createOrReplaceTempView("products")
```

A _view_ is temporary, meaning that it's automatically deleted at the end of the current session. You can also create _tables_ that are persisted in the catalog to define a database that can be queried using Spark SQL.

### Using the Spark SQL API to query data

You can use the Spark SQL API in code written in any language to query data in the catalog. For example, the following PySpark code uses a SQL query to return data from the **products** view as a dataframe.

```
bikes_df = spark.sql("SELECT ProductID, ProductName, ListPrice \
                      FROM products \
                      WHERE Category IN ('Mountain Bikes', 'Road Bikes')")
display(bikes_df)
```

The results from the code example would look similar to the following table:

| ProductID | ProductName             | ListPrice |
| --------- | ----------------------- | --------- |
| 38        | Mountain-100 Silver, 38 | 3399.9900 |
| 52        | Road-750 Black, 52      | 539.9900  |
| ...       | ...                     | ...       |

### Using SQL code

The previous example demonstrated how to use the Spark SQL API to embed SQL expressions in Spark code. In a notebook, you can also use the `%%sql` magic to run SQL code that queries objects in the catalog, like this:

```
%%sql

SELECT Category, COUNT(ProductID) AS ProductCount
FROM products
GROUP BY Category
ORDER BY Category
```

The SQL code example returns a resultset that is automatically displayed in the notebook as a table, like the one below:

|Category|ProductCount|
|---|---|
|Bib-Shorts|3|
|Bike Racks|1|
|Bike Stands|1|
|...|...|

## Modify and save dataframes

Apache Spark provides the _dataframe_ object as the primary structure for working with data. You can use dataframes to query and transform data, and persist the results in a data lake. To load data into a dataframe, you use the **spark.read** function, specifying the file format, path, and optionally the schema of the data to be read. For example, the following code loads data from all .csv files in the **orders** folder into a dataframe named **order_details** and then displays the first five records.

```
order_details = spark.read.csv('/orders/*.csv', header=True, inferSchema=True)
display(order_details.limit(5))
```
## Transform the data structure

After loading the source data into a dataframe, you can use the dataframe object's methods and Spark functions to transform it. Typical operations on a dataframe include:

- Filtering rows and columns
- Renaming columns
- Creating new columns, often derived from existing ones
- Replacing null or other values

In the following example, the code uses the `split` function to separate the values in the **CustomerName** column into two new columns named **FirstName** and **LastName**. Then it uses the `drop` method to delete the original **CustomerName** column.

```
from pyspark.sql.functions import split, col

# Create the new FirstName and LastName fields
transformed_df = order_details.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))

# Remove the CustomerName field
transformed_df = transformed_df.drop("CustomerName")

display(transformed_df.limit(5))
```

You can use the full power of the Spark SQL library to transform the data by filtering rows, deriving, removing, renaming columns, and any applying other required data modifications.

## Save the transformed data

After your dataFrame is in the required structure, you can save the results to a supported format in your data lake.

The following code example saves the dataFrame into a _parquet_ file in the data lake, replacing any existing file of the same name.

```
transformed_df.write.mode("overwrite").parquet('/transformed_data/orders.parquet')
print ("Transformed data saved!")
```