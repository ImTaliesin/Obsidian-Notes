Relational data warehouses are at the center of most enterprise business intelligence (BI) solutions. While the specific details may vary across data warehouse implementations, a common pattern based on a denormalized, multidimensional schema has emerged as the standard design for a relational data warehouse.

Like all relational databases, a data warehouse contains tables in which the data you want to analyze is stored. Most commonly, these tables are organized in a schema that is optimized for multidimensional modeling, in which numerical measures associated with events known as facts can be aggregated by the attributes of associated entities across multiple dimensions. For example, measures associated with a sales order (such as the amount paid or the quantity of items ordered) can be aggregated by attributes of the date on which the sale occurred, the customer, the store, and so on.

## Tables in a data warehouse

A common pattern for relational data warehouses is to define a schema that includes two kinds of table: _dimension_ tables and _fact_ tables.

### Dimension tables

Dimension tables describe business entities, such as products, people, places, and dates. Dimension tables contain columns for attributes of an entity. For example, a customer entity might have a first name, a last name, an email address, and a postal address (which might consist of a street address, a city, a postal code, and a country or region). In addition to attribute columns, a dimension table contains a unique key column that uniquely identifies each row in the table. In fact, it's common for a dimension table to include _**two**_ key columns:

- a _surrogate_ key that is specific to the data warehouse and uniquely identifies each row in the dimension table in the data warehouse - usually an incrementing integer number.
- An _alternate_ key, often a _natural_ or _business_ key that is used to identify a specific instance of an entity in the transactional source system from which the entity record originated - such as a product code or a customer ID.

Why have two keys? There are a few good reasons:

- The data warehouse may be populated with data from multiple source systems, which can lead to the risk of duplicate or incompatible business keys.
- Simple numeric keys generally perform better in queries that join lots of tables - a common pattern in data warehouses.
- Attributes of entities may change over time - for example, a customer might change their address. Since the data warehouse is used to support historic reporting, you may want to retain a record for each instance of an entity at multiple points in time; so that, for example, sales orders for a specific customer are counted for the city where they lived at the time the order was placed. In this case, multiple customer records would have the same business key associated with the customer, but different surrogate keys for each discrete address where the customer lived at various times.

An example of a dimension table for customer might contain the following data:

|CustomerKey|CustomerAltKey|Name|Email|Street|City|PostalCode|CountryRegion|
|---|---|---|---|---|---|---|---|
|123|I-543|Navin Jones|navin1@contoso.com|1 Main St.|Seattle|90000|United States|
|124|R-589|Mary Smith|mary2@contoso.com|234 190th Ave|Buffalo|50001|United States|
|125|I-321|Antoine Dubois|antoine1@contoso.com|2 Rue Jolie|Paris|20098|France|
|126|I-543|Navin Jones|navin1@contoso.com|24 125th Ave.|New York|50000|United States|
|...|...|...|...|...|...|...|...|
Observe that the table contains two records for _Navin Jones_. Both records use the same alternate key to identify this person (_I-543_), but each record has a different surrogate key. From this, you can surmise that the customer moved from Seattle to New York. Sales made to the customer while living in Seattle are associated with the key _123_, while purchases made after moving to New York are recorded against record _126_.

In addition to dimension tables that represent business entities, it's common for a data warehouse to include a dimension table that represents _time_. This table enables data analysts to aggregate data over temporal intervals. Depending on the type of data you need to analyze, the lowest granularity (referred to as the _grain_) of a time dimension could represent times (to the hour, second, millisecond, nanosecond, or even lower), or dates.

An example of a time dimension table with a grain at the date level might contain the following data:

|DateKey|DateAltKey|DayOfWeek|DayOfMonth|Weekday|Month|MonthName|Quarter|Year|
|---|---|---|---|---|---|---|---|---|
|19990101|01-01-1999|6|1|Friday|1|January|1|1999|
|...|...|...|...|...|...|...|...|...|
|20220101|01-01-2022|7|1|Saturday|1|January|1|2022|
|20220102|02-01-2022|1|2|Sunday|1|January|1|2022|
|...|...|...|...|...|...|...|...|...|
|20301231|31-12-2030|3|31|Tuesday|12|December|4|2030|

The timespan covered by the records in the table must include the earliest and latest points in time for any associated events recorded in a related fact table. Usually there's a record for every interval at the appropriate grain in between.

### Fact tables

Fact tables store details of observations or events; for example, sales orders, stock balances, exchange rates, or recorded temperatures. A fact table contains columns for numeric values that can be aggregated by dimensions. In addition to the numeric columns, a fact table contains key columns that reference unique keys in related dimension tables.

For example, a fact table containing details of sales orders might contain the following data:

Expand table

|OrderDateKey|CustomerKey|StoreKey|ProductKey|OrderNo|LineItemNo|Quantity|UnitPrice|Tax|ItemTotal|
|---|---|---|---|---|---|---|---|---|---|
|20220101|123|5|701|1001|1|2|2.50|0.50|5.50|
|20220101|123|5|765|1001|2|1|2.00|0.20|2.20|
|20220102|125|2|723|1002|1|1|4.99|0.49|5.48|
|20220103|126|1|823|1003|1|1|7.99|0.80|8.79|
|...|...|...|...|...|...|...|...|...|...|

A fact table's dimension key columns determine its grain. For example, the sales orders fact table includes keys for dates, customers, stores, and products. An order might include multiple products, so the grain represents line items for individual products sold in stores to customers on specific days.

## Data warehouse schema designs

In most transactional databases that are used in business applications, the data is _normalized_ to reduce duplication. In a data warehouse however, the dimension data is generally _de-normalized_ to reduce the number of joins required to query the data.

Often, a data warehouse is organized as a _star_ schema, in which a fact table is directly related to the dimension tables, as shown in this example:

![A diagram showing a star schema.](https://learn.microsoft.com/en-us/training/wwl-data-ai/design-multidimensional-schema-to-optimize-analytical-workloads/media/star-schema.png)

The attributes of an entity can be used to aggregate measures in fact tables over multiple hierarchical levels - for example, to find total sales revenue by country or region, city, postal code, or individual customer. The attributes for each level can be stored in the same dimension table. However, when an entity has a large number of hierarchical attribute levels, or when some attributes can be shared by multiple dimensions (for example, both customers and stores have a geographical address), it can make sense to apply some normalization to the dimension tables and create a _snowflake_ schema, as shown in the following example:

![A diagram showing a snowflake schema.](https://learn.microsoft.com/en-us/training/wwl-data-ai/design-multidimensional-schema-to-optimize-analytical-workloads/media/snowflake-schema.png)

In this case, the **DimProduct** table has been normalized to create separate dimension tables for product categories and suppliers, and a **DimGeography** table has been added to represent geographical attributes for both customers and stores. Each row in the **DimProduct** table contains key values for the corresponding rows in the **DimCategory** and **DimSupplier** tables; and each row in the **DimCustomer** and **DimStore** tables contains a key value for the corresponding row in the **DimGeography** table.

Great question! Let's break down the differences between fact tables and dimension tables:

Fact Tables:

1. Content: Contain quantitative data, or "facts" about business processes
2. Examples: Sales amounts, quantities sold, costs, prices
3. Nature: Typically numeric and can be aggregated
4. Size: Usually the largest tables in the database
5. Updates: Frequently updated as new transactions occur
6. Granularity: Each row represents a specific event or transaction
7. Keys: Contains foreign keys that link to dimension tables

Dimension Tables:

1. Content: Contain descriptive attributes about the data in fact tables
2. Examples: Product details, customer information, time periods, store locations
3. Nature: Usually textual or categorical
4. Size: Typically smaller than fact tables
5. Updates: Change less frequently, sometimes called "slowly changing dimensions"
6. Granularity: Each row represents a unique entity or attribute
7. Keys: Contains primary keys that are referenced by fact tables

![[Pasted image 20240903232241.png]]

## Creating a dedicated SQL pool

To create a relational data warehouse in Azure Synapse Analytics, you must create a dedicated SQL Pool. The simplest way to do this in an existing Azure Synapse Analytics workspace is to use the **Manage** page in Azure Synapse Studio, as shown here:

![A screenshot of the SQL pools tab in the Manage page of Synapse Studio.](https://learn.microsoft.com/en-us/training/wwl-data-ai/design-multidimensional-schema-to-optimize-analytical-workloads/media/sql-pools.png)

When provisioning a dedicated SQL pool, you can specify the following configuration settings:

- A unique name for the dedicated SQL pool.
- A performance level for the SQL pool, which can range from _DW100c_ to _DW30000c_ and which determines the cost per hour for the pool when it's running.
- Whether to start with an empty pool or restore an existing database from a backup.
- The _collation_ of the SQL pool, which determines sort order and string comparison rules for the database. (_You can't change the collation after creation_).

After creating a dedicated SQL pool, you can control its running state in the **Manage** page of Synapse Studio; pausing it when not required to prevent unnecessary costs.

When the pool is running, you can explore it on the **Data** page, and create SQL scripts to run in it.

## Create data warehouse tables
To create a relational data warehouse in Azure Synapse Analytics, you must create a dedicated SQL Pool. The simplest way to do this in an existing Azure Synapse Analytics workspace is to use the **Manage** page in Azure Synapse Studio, as shown here:

![A screenshot of the SQL pools tab in the Manage page of Synapse Studio.](https://learn.microsoft.com/en-us/training/wwl-data-ai/design-multidimensional-schema-to-optimize-analytical-workloads/media/sql-pools.png)

When provisioning a dedicated SQL pool, you can specify the following configuration settings:

- A unique name for the dedicated SQL pool.
- A performance level for the SQL pool, which can range from _DW100c_ to _DW30000c_ and which determines the cost per hour for the pool when it's running.
- Whether to start with an empty pool or restore an existing database from a backup.
- The _collation_ of the SQL pool, which determines sort order and string comparison rules for the database. (_You can't change the collation after creation_).

After creating a dedicated SQL pool, you can control its running state in the **Manage** page of Synapse Studio; pausing it when not required to prevent unnecessary costs.


When the pool is running, you can explore it on the **Data** page, and create SQL scripts to run in it.
### Considerations for creating tables

To create tables in the dedicated SQL pool, you use the `CREATE TABLE` (or sometimes the `CREATE EXTERNAL TABLE`) Transact-SQL statement. The specific options used in the statement depend on the type of table you're creating, which can include:

- Fact tables
- Dimension tables
- Staging tables

The data warehouse is composed of _fact_ and _dimension_ tables as discussed previously. _Staging_ tables are often used as part of the data warehousing loading process to ingest data from source systems.

When designing a star schema model for small or medium sized datasets you can use your preferred database, such as Azure SQL. For larger data sets you may benefit from implementing your data warehouse in Azure Synapse Analytics instead of SQL Server. It's important to understand some key differences when creating tables in Synapse Analytics.
### Data integrity constraints

Dedicated SQL pools in Synapse Analytics don't support _foreign key_ and _unique_ constraints as found in other relational database systems like SQL Server. This means that jobs used to load data must maintain uniqueness and referential integrity for keys, without relying on the table definitions in the database to do so.

For more information about constraints in Azure Synapse Analytics dedicated SQL pools, see [Primary key, foreign key, and unique key using dedicated SQL pool in Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-table-constraints).
### Indexes
While Synapse Analytics dedicated SQL pools support _clustered_ indexes as found in SQL Server, the default index type is _clustered columnstore_. This index type offers a significant performance advantage when querying large quantities of data in a typical data warehouse schema and should be used where possible. However, some tables may include data types that can't be included in a clustered columnstore index (for example, VARBINARY(MAX)), in which case a clustered index can be used instead.

For more information about indexing in Azure Synapse Analytics dedicated SQL pools, see [Indexes on dedicated SQL pool tables in Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-index).

### Distribution
Azure Synapse Analytics dedicated SQL pools use a [massively parallel processing (MPP) architecture](https://learn.microsoft.com/en-us/azure/architecture/data-guide/relational-data/data-warehousing#data-warehousing-in-azure), as opposed to the symmetric multiprocessing (SMP) architecture used in most OLTP database systems. In an MPP system, the data in a table is distributed for processing across a pool of nodes. Synapse Analytics supports the following kinds of distribution:

- **Hash**: A deterministic hash value is calculated for the specified column and used to assign the row to a compute node.
- **Round-robin**: Rows are distributed evenly across all compute nodes.
- **Replicated**: A copy of the table is stored on each compute node.

The table type often determines which option to choose for distributing the table.

| Table type | Recommended distribution option                                                                                                                                                               |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| #Dimension | Use replicated distribution for smaller tables to avoid data shuffling when joining to distributed fact tables. If tables are too large to store on each compute node, use hash distribution. |
| #Fact      | Use hash distribution with clustered columnstore index to distribute fact tables across compute nodes.                                                                                        |
| #Staging   | Use round-robin distribution for staging tables to evenly distribute data across compute nodes.                                                                                               |
### Creating dimension tables

When you create a dimension table, ensure that the table definition includes surrogate and alternate keys as well as columns for the attributes of the dimension that you want to use to group aggregations. It's often easiest to use an `IDENTITY` column to auto-generate an incrementing surrogate key (otherwise you need to generate unique keys every time you load data). The following example shows a `CREATE TABLE` statement for a hypothetical **DimCustomer** dimension table.

```
CREATE TABLE dbo.DimCustomer
(
    CustomerKey INT IDENTITY NOT NULL,
    CustomerAlternateKey NVARCHAR(15) NULL,
    CustomerName NVARCHAR(80) NOT NULL,
    EmailAddress NVARCHAR(50) NULL,
    Phone NVARCHAR(25) NULL,
    StreetAddress NVARCHAR(100),
    City NVARCHAR(20),
    PostalCode NVARCHAR(10),
    CountryRegion NVARCHAR(20)
)
WITH
(
    DISTRIBUTION = REPLICATE,
    CLUSTERED COLUMNSTORE INDEX
);
```

If desired, you can create a specific _schema_ as a namespace for your tables. In this example, the default **dbo** schema is used.

If you intend to use a _snowflake_ schema in which dimension tables are related to one another, you should include the key for the _parent_ dimension in the definition of the _child_ dimension table. For example, the following SQL code could be used to move the geographical address details from the **DimCustomer** table to a separate **DimGeography** dimension table:

```
CREATE TABLE dbo.DimGeography
(
    GeographyKey INT IDENTITY NOT NULL,
    GeographyAlternateKey NVARCHAR(10) NULL,
    StreetAddress NVARCHAR(100),
    City NVARCHAR(20),
    PostalCode NVARCHAR(10),
    CountryRegion NVARCHAR(20)
)
WITH
(
    DISTRIBUTION = REPLICATE,
    CLUSTERED COLUMNSTORE INDEX
);

CREATE TABLE dbo.DimCustomer
(
    CustomerKey INT IDENTITY NOT NULL,
    CustomerAlternateKey NVARCHAR(15) NULL,
    GeographyKey INT NULL,
    CustomerName NVARCHAR(80) NOT NULL,
    EmailAddress NVARCHAR(50) NULL,
    Phone NVARCHAR(25) NULL
)
WITH
(
    DISTRIBUTION = REPLICATE,
    CLUSTERED COLUMNSTORE INDEX
);
```

 The tables are connected through the GeographyKey column:
    - DimGeography has GeographyKey as its primary key (IDENTITY column).
    - DimCustomer has GeographyKey as a foreign key referencing DimGeography.
- How it works:
    - Instead of storing all customer information in one table, the address details are moved to a separate DimGeography table.
    - Each unique combination of address details gets a unique GeographyKey in DimGeography.
    - In DimCustomer, instead of storing the full address, we only store the GeographyKey that references the corresponding row in DimGeography.

- - In the DimGeography table:
     `GeographyKey INT IDENTITY NOT NULL`
        This creates an auto-incrementing unique identifier for each geography entry.
    - In the DimCustomer table:
     `GeographyKey INT NULL`
        This column will store the corresponding GeographyKey from the DimGeography table.
- IDENTITY and Column Names: The IDENTITY property and column names are separate concepts:
    - IDENTITY: This is a property in SQL Server that automatically generates unique, incrementing values for a column. It's typically used for primary keys.

### Time dimension tables

These are special dimension tables that allow for time-based analysis. They typically include various attributes related to dates.

Most data warehouses include a _time_ dimension table that enables you to aggregate data by multiple hierarchical levels of time interval. For example, the following example creates a **DimDate** table with attributes that relate to specific dates.

```
CREATE TABLE dbo.DimDate
( 
    DateKey INT NOT NULL,
    DateAltKey DATETIME NOT NULL,
    DayOfMonth INT NOT NULL,
    DayOfWeek INT NOT NULL,
    DayName NVARCHAR(15) NOT NULL,
    MonthOfYear INT NOT NULL,
    MonthName NVARCHAR(15) NOT NULL,
    CalendarQuarter INT  NOT NULL,
    CalendarYear INT NOT NULL,
    FiscalQuarter INT NOT NULL,
    FiscalYear INT NOT NULL
)
WITH
(
    DISTRIBUTION = REPLICATE,
    CLUSTERED COLUMNSTORE INDEX
);
```

### Creating fact tables

Fact tables include the keys for each dimension to which they're related, and the attributes and numeric measures for specific events or observations that you want to analyze.

The following code example creates a hypothetical fact table named **FactSales** that is related to multiple dimensions through key columns (date, customer, product, and store)

```
CREATE TABLE dbo.FactSales
(
    OrderDateKey INT NOT NULL,
    CustomerKey INT NOT NULL,
    ProductKey INT NOT NULL,
    StoreKey INT NOT NULL,
    OrderNumber NVARCHAR(10) NOT NULL,
    OrderLineItem INT NOT NULL,
    OrderQuantity SMALLINT NOT NULL,
    UnitPrice DECIMAL NOT NULL,
    Discount DECIMAL NOT NULL,
    Tax DECIMAL NOT NULL,
    SalesAmount DECIMAL NOT NULL
)
WITH
(
    DISTRIBUTION = HASH(OrderNumber),
    CLUSTERED COLUMNSTORE INDEX
);
```

### Creating staging tables

Staging tables are used as temporary storage for data as it's being loaded into the data warehouse. A typical pattern is to structure the table to make it as efficient as possible to ingest the data from its external source (often files in a data lake) into the relational database, and then use SQL statements to load the data from the staging tables into the dimension and fact tables.

The following code example creates a staging table for product data that will ultimately be loaded into a dimension table:

```
CREATE TABLE dbo.StageProduct
(
    ProductID NVARCHAR(10) NOT NULL,
    ProductName NVARCHAR(200) NOT NULL,
    ProductCategory NVARCHAR(200) NOT NULL,
    Color NVARCHAR(10),
    Size NVARCHAR(10),
    ListPrice DECIMAL NOT NULL,
    Discontinued BIT NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

### Using external tables

In some cases, if the data to be loaded is in files with an appropriate structure, it can be more effective to create external tables that reference the file location. This way, the data can be read directly from the source files instead of being loaded into the relational store. The following example, shows how to create an external table that references files in the data lake associated with the Synapse workspace:

```

-- External data source links to data lake location
CREATE EXTERNAL DATA SOURCE StagedFiles
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/stagedfiles/'
);
GO

-- External format specifies file format
CREATE EXTERNAL FILE FORMAT ParquetFormat
WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

-- External table references files in external data source
CREATE EXTERNAL TABLE dbo.ExternalStageProduct
(
    ProductID NVARCHAR(10) NOT NULL,
    ProductName NVARCHAR(200) NOT NULL,
    ProductCategory NVARCHAR(200) NOT NULL,
    Color NVARCHAR(10),
    Size NVARCHAR(10),
    ListPrice DECIMAL NOT NULL,
    Discontinued BIT NOT NULL
)
WITH
(
    DATA_SOURCE = StagedFiles,
    LOCATION = 'products/*.parquet',
    FILE_FORMAT = ParquetFormat
);
GO
```

## Load data warehous tables
loading a data warehouse is typically achieved by adding new data from files in a data lake into tables in the data warehouse. The `COPY` statement is an effective way to accomplish this task, as shown in the following example:

```
COPY INTO dbo.StageProducts
    (ProductID, ProductName, ProductCategory, Color, Size, ListPrice, Discontinued)
FROM 'https://mydatalake.blob.core.windows.net/data/stagedfiles/products/*.parquet'
WITH
(
    FILE_TYPE = 'PARQUET',
    MAXERRORS = 0,
    IDENTITY_INSERT = 'OFF'
);
```
Let's walk through a practical example of how data might flow through different table types:

a) Source Systems -> Data Lake:
- Data from various operational systems (e.g., sales, inventory, customer management) is extracted and stored in raw format in a data lake.
- Files might be in formats like CSV, JSON, or Parquet.

b) Data Lake -> Staging Tables:

- Raw data is loaded into staging tables using the COPY command you mentioned.
- Example:
    `COPY INTO dbo.StageSales FROM 'https://mydatalake.blob.core.windows.net/data/sales/*.parquet' WITH (FILE_TYPE = 'PARQUET');`

c) Staging Tables -> Dimension Tables:

- Data is transformed and loaded into dimension tables.
- This might involve looking up existing records, inserting new ones, and handling slowly changing dimensions.
- Example:
    `MERGE INTO dbo.DimProduct AS target USING dbo.StageProducts AS source ON target.ProductID = source.ProductID WHEN MATCHED THEN     UPDATE SET target.ProductName = source.ProductName, ... WHEN NOT MATCHED THEN     INSERT (ProductID, ProductName, ...)    VALUES (source.ProductID, source.ProductName, ...);`
d) Staging Tables -> Fact Tables:

- After dimension tables are updated, fact data is loaded.
- This involves looking up dimension keys and aggregating measures.
- Example:
    `INSERT INTO dbo.FactSales (DateKey, ProductKey, CustomerKey, SalesAmount) SELECT      d.DateKey,    p.ProductKey,    c.CustomerKey,    s.SalesAmount FROM dbo.StageSales s JOIN dbo.DimDate d ON s.SaleDate = d.FullDate JOIN dbo.DimProduct p ON s.ProductID = p.ProductID JOIN dbo.DimCustomer c ON s.CustomerID = c.CustomerID;`

1. External Tables vs. Staging Tables:

External Tables:
- Provide a way to query data stored outside the data warehouse (e.g., in the data lake) without actually loading it.
- Useful for exploring raw data, performing light transformations, or querying large datasets that don't need to be fully loaded.
- Example:
    `CREATE EXTERNAL TABLE ExtSales (     SaleID int,    SaleDate date,    ProductID int,    Quantity int,    Amount decimal(10,2) ) WITH (     LOCATION = 'sales/*.parquet',    DATA_SOURCE = MyDataLake,    FILE_FORMAT = ParquetFileFormat );`

Staging Tables:
- Temporary holding area for data that will be processed and loaded into the actual warehouse tables.
- Allow for data validation, transformation, and error handling before final loading.
- Typically internal to the data warehouse.

Best Practices for Choosing:
- Use external tables when you need to query data in the lake without loading it, or for initial data exploration.
- Use staging tables when you need to perform complex transformations, data quality checks, or when the data will be loaded into the warehouse regularly.
- Consider using external tables for large historical datasets that are rarely queried, to save storage in the data warehouse.
- Use staging tables when you need to join data from multiple sources before loading into dimension or fact tables.

3. Additional Insights:
- Incremental Loading: For large datasets, consider implementing incremental loading to only process new or changed data each time.
- Error Handling: Implement error handling and logging in your ETL process to catch and manage data quality issues.
- Optimizing Performance: After loading, update statistics and rebuild indexes to ensure query performance.
- Data Validation: Use staging tables to perform data quality checks before loading into final tables.

This process allows for a structured, manageable approach to building and maintaining a data warehouse, ensuring data integrity and performance. The combination of external tables for flexibility and staging tables for controlled data loading provides a robust architecture for handling various data scenarios.

## Query a data warehouse
When the dimension and fact tables in a data warehouse have been loaded with data, you can use SQL to query the tables and analyze the data they contain. The Transact-SQL syntax used to query tables in a Synapse dedicated SQL pool is similar to SQL used in SQL Server or Azure SQL Database.
### Aggregating measures by dimension attributes

Most data analytics with a data warehouse involves aggregating numeric measures in fact tables by attributes in dimension tables. Because of the way a star or snowflake schema is implemented, queries to perform this kind of aggregation rely on `JOIN` clauses to connect fact tables to dimension tables, and a combination of aggregate functions and `GROUP BY` clauses to define the aggregation hierarchies.

For example, the following SQL queries the **FactSales** and **DimDate** tables in a hypothetical data warehouse to aggregate sales amounts by year and quarter:

SQLCopy

```
SELECT  dates.CalendarYear,
        dates.CalendarQuarter,
        SUM(sales.SalesAmount) AS TotalSales
FROM dbo.FactSales AS sales
JOIN dbo.DimDate AS dates ON sales.OrderDateKey = dates.DateKey
GROUP BY dates.CalendarYear, dates.CalendarQuarter
ORDER BY dates.CalendarYear, dates.CalendarQuarter;
```

The results from this query would look similar to the following table:

Expand table

|CalendarYear|CalendarQuarter|TotalSales|
|---|---|---|
|2020|1|25980.16|
|2020|2|27453.87|
|2020|3|28527.15|
|2020|4|31083.45|
|2021|1|34562.96|
|2021|2|36162.27|
|...|...|...|

You can join as many dimension tables as needed to calculate the aggregations you need. For example, the following code extends the previous example to break down the quarterly sales totals by city based on the customer's address details in the **DimCustomer** table:

SQLCopy

```
SELECT  dates.CalendarYear,
        dates.CalendarQuarter,
        custs.City,
        SUM(sales.SalesAmount) AS TotalSales
FROM dbo.FactSales AS sales
JOIN dbo.DimDate AS dates ON sales.OrderDateKey = dates.DateKey
JOIN dbo.DimCustomer AS custs ON sales.CustomerKey = custs.CustomerKey
GROUP BY dates.CalendarYear, dates.CalendarQuarter, custs.City
ORDER BY dates.CalendarYear, dates.CalendarQuarter, custs.City;
```

This time, the results include a quarterly sales total for each city:

Expand table

|CalendarYear|CalendarQuarter|City|TotalSales|
|---|---|---|---|
|2020|1|Amsterdam|5982.53|
|2020|1|Berlin|2826.98|
|2020|1|Chicago|5372.72|
|...|...|...|..|
|2020|2|Amsterdam|7163.93|
|2020|2|Berlin|8191.12|
|2020|2|Chicago|2428.72|
|...|...|...|..|
|2020|3|Amsterdam|7261.92|
|2020|3|Berlin|4202.65|
|2020|3|Chicago|2287.87|
|...|...|...|..|
|2020|4|Amsterdam|8262.73|
|2020|4|Berlin|5373.61|
|2020|4|Chicago|7726.23|
|...|...|...|..|
|2021|1|Amsterdam|7261.28|
|2021|1|Berlin|3648.28|
|2021|1|Chicago|1027.27|
|...|...|...|..|

### Joins in a snowflake schema

When using a snowflake schema, dimensions may be partially normalized; requiring multiple joins to relate fact tables to snowflake dimensions. For example, suppose your data warehouse includes a **DimProduct** dimension table from which the product categories have been normalized into a separate **DimCategory** table. A query to aggregate items sold by product category might look similar to the following example:

SQLCopy

```
SELECT  cat.ProductCategory,
        SUM(sales.OrderQuantity) AS ItemsSold
FROM dbo.FactSales AS sales
JOIN dbo.DimProduct AS prod ON sales.ProductKey = prod.ProductKey
JOIN dbo.DimCategory AS cat ON prod.CategoryKey = cat.CategoryKey
GROUP BY cat.ProductCategory
ORDER BY cat.ProductCategory;
```

The results from this query include the number of items sold for each product category:

Expand table

|ProductCategory|ItemsSold|
|---|---|
|Accessories|28271|
|Bits and pieces|5368|
|...|...|

 Note

JOIN clauses for **FactSales** and **DimProduct** and for **DimProduct** and **DimCategory** are both required, even though no fields from **DimProduct** are returned by the query.

#### Using ranking functions
Another common kind of analytical query is to partition the results based on a dimension attribute and _rank_ the results within each partition. For example, you might want to rank stores each year by their sales revenue. To accomplish this goal, you can use Transact-SQL _ranking_ functions such as `ROW_NUMBER`, `RANK`, `DENSE_RANK`, and `NTILE`. These functions enable you to partition the data over categories, each returning a specific value that indicates the relative position of each row within the partition:

- **ROW_NUMBER** returns the ordinal position of the row within the partition. For example, the first row is numbered 1, the second 2, and so on.
- **RANK** returns the ranked position of each row in the ordered results. For example, in a partition of stores ordered by sales volume, the store with the highest sales volume is ranked 1. If multiple stores have the same sales volumes, they'll be ranked the same, and the rank assigned to subsequent stores reflects the number of stores that have higher sales volumes - including ties.
- **DENSE_RANK** ranks rows in a partition the same way as **RANK**, but when multiple rows have the same rank, subsequent rows are ranking positions ignore ties.
- **NTILE** returns the specified percentile in which the row falls. For example, in a partition of stores ordered by sales volume, `NTILE(4)` returns the quartile in which a store's sales volume places it.

For example, consider the following query:
```
SELECT  ProductCategory,
        ProductName,
        ListPrice,
        ROW_NUMBER() OVER
            (PARTITION BY ProductCategory ORDER BY ListPrice DESC) AS RowNumber,
        RANK() OVER
            (PARTITION BY ProductCategory ORDER BY ListPrice DESC) AS Rank,
        DENSE_RANK() OVER
            (PARTITION BY ProductCategory ORDER BY ListPrice DESC) AS DenseRank,
        NTILE(4) OVER
            (PARTITION BY ProductCategory ORDER BY ListPrice DESC) AS Quartile
FROM dbo.DimProduct
ORDER BY ProductCategory;
```

The query partitions products into groupings based on their categories, and within each category partition, the relative position of each product is determined based on its list price. The results from this query might look similar to the following table:

|ProductCategory|ProductName|ListPrice|RowNumber|Rank|DenseRank|Quartile|
|---|---|---|---|---|---|---|
|Accessories|Widget|8.99|1|1|1|1|
|Accessories|Knicknak|8.49|2|2|2|1|
|Accessories|Sprocket|5.99|3|3|3|2|
|Accessories|Doodah|5.99|4|3|3|2|
|Accessories|Spangle|2.99|5|5|4|3|
|Accessories|Badabing|0.25|6|6|5|4|
|Bits and pieces|Flimflam|7.49|1|1|1|1|
|Bits and pieces|Snickity wotsit|6.99|2|2|2|1|
|Bits and pieces|Flange|4.25|3|3|3|2|
|...|...|...|...|...|...|...|

The sample results demonstrate the difference between `RANK` and `DENSE_RANK`. Note that in the _Accessories_ category, the _Sprocket_ and _Doodah_ products have the same list price; and are both ranked as the 3rd highest priced product. The next highest priced product has a _RANK_ of 5 (there are four products more expensive than it) and a _DENSE_RANK_ of 4 (there are three higher prices).

To learn more about ranking functions, see [Ranking Functions (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/functions/ranking-functions-transact-sql) in the Azure Synapse Analytics documentation.

## Retrieving an approximate count

While the purpose of a data warehouse is primarily to support analytical data models and reports for the enterprise; data analysts and data scientists often need to perform some initial data exploration, just to determine the basic scale and distribution of the data.

For example, the following query uses the `COUNT` function to retrieve the number of sales for each year in a hypothetical data warehouse:


```
SELECT dates.CalendarYear AS CalendarYear,
    COUNT(DISTINCT sales.OrderNumber) AS Orders
FROM FactSales AS sales
JOIN DimDate AS dates ON sales.OrderDateKey = dates.DateKey
GROUP BY dates.CalendarYear
ORDER BY CalendarYear;
```

The results of this query might look similar to the following table:

|CalendarYear|Orders|
|---|---|
|2019|239870|
|2020|284741|
|2021|309272|
|...|...|

The volume of data in a data warehouse can mean that even simple queries to count the number of records that meet specified criteria can take a considerable time to run. In many cases, a precise count isn't required - an approximate estimate will suffice. In such cases, you can use the `APPROX_COUNT_DISTINCT` function as shown in the following example:


```
SELECT dates.CalendarYear AS CalendarYear,
    APPROX_COUNT_DISTINCT(sales.OrderNumber) AS ApproxOrders
FROM FactSales AS sales
JOIN DimDate AS dates ON sales.OrderDateKey = dates.DateKey
GROUP BY dates.CalendarYear
ORDER BY CalendarYear;
```

The `APPROX_COUNT_DISTINCT` function uses a _HyperLogLog_ algorithm to retrieve an approximate count. The result is guaranteed to have a maximum error rate of 2% with 97% probability, so the results of this query with the same hypothetical data as before might look similar to the following table:

|CalendarYear|ApproxOrders|
|---|---|
|2019|235552|
|2020|290436|
|2021|304633|
|...|...|

The counts are less accurate, but still sufficient for an approximate comparison of yearly sales. With a large volume of data, the query using the `APPROX_COUNT_DISTINCT` function completes more quickly, and the reduced accuracy may be an acceptable trade-off during basic data exploration.