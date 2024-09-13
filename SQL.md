SQL includes many features and functions that enable you to manipulate data. For example, you can use SQL to:

- Filter rows and columns in a dataset.
- Rename data fields and convert between data types.
- Calculate derived data fields.
- Manipulate string values.
- Group and aggregate data.
## Data Types
### Numerical
Integer data types hold whole numbers. There are 4 int types:
* INT: range = -2,147,483,648, -2,147,483,647
* TINYINT: -128 - 127
* SMALLINT:
* BIGINT:
4 decimal types:
* FLOAT: standard
* DOUBLE: more precise but is big file size
* DECIMAL: exact numerical places, very big file size.
### String
### Date/ Time
The DATE Data type is used to store date values in this format "YYYY-MM-DD"
### Boolean


## Vocab

### SELECT
* used to select data
* `select * from ___

if you want unique data use `select distinct`

  
### Catalog
* You can specify a catalog by using catalog.schema.table
	* `select * from samples.nyctaxi.trips`
* Or by typing `use catalog _____`
*   `show catalogs;`
* `describe catalog extended dbw_sql_udemytutorial;`
* You can create a catalog in the data tab and you need admin priv.

### Schema

### CREATE 
You can create a table and copy another table with the same query.
* `CREATE TABLE TEST_2 AS SELECT * FROM TEST_1`

### CAST 
CAST is a SQL function used to convert data from one data type to another. It's useful when you need to change the data type of a column or expression in your query results.
- **Purpose:** Convert data between compatible data types.
- **Syntax:** `CAST(expression AS data_type)`
#### Common Use Cases
- Converting numbers to strings (or vice versa)
- Changing date formats
- Adjusting precision of numeric data
- Converting Boolean values to integers
#### Examples
Converting a string to a date
`SELECT CAST('2023-09-12' AS DATE) AS converted_date;
Changing a number to a string
`SELECT CAST(12345 AS VARCHAR(10)) AS number_as_string;
Converting a date to a string
`SELECT CAST(GETDATE() AS VARCHAR(23)) AS date_as_string;`

### TRUNCATE
you can delete all rows from a table but keep the schema by using truncate
`truncate table _tablename_;`

### ALTER
alter table command can 
* `RENAME TO`
* `ADD COLUMN _type_`
* `DROP COLUMN`

### IDENTITY (auto incrementing)
1. `IDENTITY`: This is a crucial part for auto-incrementing.
    - When you add `IDENTITY` to a column definition, SQL Server automatically generates unique, incrementing values for this column whenever a new row is inserted.
    - By default, it starts at 1 and increments by 1 for each new row.
    - You don't need to specify a value for this column when inserting data; SQL Server handles it automatically.
2. `NULL` / `NOT NULL`:
    - `NOT NULL` means the column must always have a value.
    - `NULL` means the column can have no value (i.e., be empty).
So, let's compare:
1. `GeographyKey INT IDENTITY NOT NULL` (in DimGeography table)
    - This creates an auto-incrementing primary key.
    - Every new row will automatically get a unique, incrementing integer value.
    - This column cannot be empty (NOT NULL).
    - Example: If you insert three rows without specifying GeographyKey, they might get values 1, 2, and 3 automatically.
2. `GeographyKey INT NULL` (in DimCustomer table)
    - This is a regular integer column that can contain NULL values.
    - It doesn't auto-increment.
    - You need to manually specify the value when inserting data, or it will be NULL.
    - This is used as a foreign key to reference the DimGeography table.

To illustrate:
```
-- This will auto-generate GeographyKey values
INSERT INTO DimGeography (StreetAddress, City, PostalCode, CountryRegion)
VALUES ('123 Main St', 'Springfield', '12345', 'USA');

-- The GeographyKey must be specified (or left NULL)
INSERT INTO DimCustomer (GeographyKey, CustomerName, EmailAddress)
VALUES (1, 'John Doe', 'john@example.com');
```

In the DimGeography insert, you don't specify GeographyKey because it's an IDENTITY column. In the DimCustomer insert, you do need to specify it (or omit it to allow NULL) because it's not an IDENTITY column in this table.

------------------------------------------------------------------

## Auth
Serverless SQL pool authentication refers to how users prove their identity when connecting to the endpoint. Two types of authentication are supported:

- **SQL Authentication**
    This authentication method uses a username and password.
- **Microsoft Entra authentication**
    This authentication method uses identities managed by Microsoft Entra ID. For Microsoft Entra users, multi-factor authentication can be enabled. Use Active Directory authentication (integrated security) whenever possible.

If SQL Authentication is used, the SQL user exists only in the serverless SQL pool and permissions are scoped to the objects in the serverless SQL pool. Access to securable objects in other services (such as Azure Storage) can't be granted to a SQL user directly since it only exists in scope of serverless SQL pool. The SQL user needs get authorization to access the files in the storage account.

If Microsoft Entra authentication is used, a user can sign in to a serverless SQL pool and other services, like Azure Storage, and can grant permissions to the Microsoft Entra user.

### Access to storage accounts
A user that is logged into the serverless SQL pool service must be authorized to access and query the files in Azure Storage. Serverless SQL pool supports the following authorization types:

- Anonymous access
    
    To access publicly available files placed on Azure storage accounts that allow anonymous access.
    
- Shared access signature (SAS)
    
    Provides delegated access to resources in storage account. With a SAS, you can grant clients access to resources in storage account, without sharing account keys. A SAS gives you granular control over the type of access you grant to clients who have the SAS: validity interval, granted permissions, acceptable IP address range, acceptable protocol (https/http).
    
- Managed Identity.
    
    Is a feature of Microsoft Entra ID that provides Azure services for serverless SQL pool. Also, it deploys an automatically managed identity in Microsoft Entra ID. This identity can be used to authorize the request for data access in Azure Storage. Before accessing the data, the Azure Storage administrator must grant permissions to Managed Identity for accessing the data. Granting permissions to Managed Identity is done the same way as granting permission to any other Microsoft Entra user.
    
- User Identity
    
    Also known as "pass-through", is an authorization type where the identity of the Microsoft Entra user that logged into serverless SQL pool is used to authorize access to the data. Before accessing the data, Azure Storage administrator must grant permissions to Microsoft Entra user for accessing the data. This authorization type uses the Microsoft Entra user that logged into serverless SQL pool, therefore it's not supported for SQL user types.
    
Supported authorization types for database users can be found in the table below:

|Authorization type|SQL user|Microsoft Entra user|
|---|---|---|
|User Identity|Not supported|Supported|
|SAS|Supported|Supported|
|Managed Identity|Not supported|Supported|

Supported storage and authorization types can be found in the table below:

| Authorization type | Blob Storage                                                                            | ADLS Gen1     | ADLS Gen2                                                                               |
| ------------------ | --------------------------------------------------------------------------------------- | ------------- | --------------------------------------------------------------------------------------- |
| User Identity      | Supported - SAS token can be used to access storage that is not protected with firewall | Not supported | Supported - SAS token can be used to access storage that is not protected with firewall |
| SAS                | Supported                                                                               | Supported     | Supported                                                                               |
| Managed Identity   | Supported                                                                               | Supported     | Supported                                                                               |

### Manage user permissions in Azure Synapse serverless SQL pools
To secure data, Azure Storage implements an access control model that supports both Azure role-based access control (Azure RBAC) and access control lists (ACLs) like Portable Operating System Interface for Unix (POSIX)

You can associate a security principal with an access level for files and directories. These associations are captured in an access control list (ACL). Each file and directory in your storage account has an access control list. When a security principal attempts an operation on a file or directory, an ACL check determines whether that security principal (user, group, service principal, or managed identity) has the correct permission level to perform the operation.

There are two kinds of access control lists:

- **Access ACLs**
    
    Controls access to an object. Files and directories both have access ACLs.
    
- **Default ACLs**
    
    Are templates of ACLs associated with a directory that determine the access ACLs for any child items that are created under that directory. Files do not have default ACLs.
    

Both access ACLs and default ACLs have the same structure.

The permissions on a container object are Read, Write, and Execute, and they can be used on files and directories as shown in the following table:

**Levels of permissions**

|Permission|File|Directory|
|---|---|---|
|Read (R)|Can read the contents of a file|Requires Read and Execute to list the contents of the directory|
|Write (W)|Can write or append to a file|Requires Write and Execute to create child items in a directory|
|Execute (X)|Does not mean anything in the context of Data Lake Storage Gen2|Required to traverse the child items of a directory|
## Guidelines in setting up ACLs

Always use Microsoft Entra security groups as the assigned principal in an ACL entry. Resist the opportunity to directly assign individual users or service principals. Using this structure will allow you to add and remove users or service principals without the need to reapply ACLs to an entire directory structure. Instead, you can just add or remove users and service principals from the appropriate Microsoft Entra security group.

There are many ways to set up groups. For example, imagine that you have a directory named **/LogData** which holds log data that is generated by your server. Azure Data Factory (ADF) ingests data into that folder. Specific users from the service engineering team will upload logs and manage other users of this folder, and various Databricks clusters will analyze logs from that folder.

To enable these activities, you could create a LogsWriter group and a LogsReader group. Then, you could assign permissions as follows:

- Add the LogsWriter group to the ACL of the **/LogData** directory with rwx permissions.
- Add the LogsReader group to the ACL of the **/LogData** directory with r-x permissions.
- Add the service principal object or Managed Service Identity (MSI) for ADF to the LogsWriters group.
- Add users in the service engineering team to the LogsWriter group.
- Add the service principal object or MSI for Databricks to the LogsReader group.

If a user in the service engineering team leaves the company, you could just remove them from the LogsWriter group. If you did not add that user to a group, but instead, you added a dedicated ACL entry for that user, you would have to remove that ACL entry from the **/LogData** directory. You would also have to remove the entry from all subdirectories and files in the entire directory hierarchy of the **/LogData** directory.

## Roles necessary for serverless SQL pool users

For users which need **read only** access you should assign role named **Storage Blob Data Reader**.

For users which need **read/write** access you should assign role named **Storage Blob Data Contributor**. Read/Write access is needed if user should have access to create external table as select (CETAS).

**If user has a role Owner or Contributor, that role is not enough. Azure Data Lake Storage gen 2 has super-roles which should be assigned.**
## Database level permission

To provide more granular access to the user, you should use Transact-SQL syntax to create logins and users.

To grant access to a user to a single serverless SQL pool database, follow the steps in this example:

1. Create LOGIN
    ```
    use master
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    ```
2. Create USER
    ```
    use yourdb -- Use your DB name
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```
    
3. Add USER to members of the specified role
    ```
    use yourdb -- Use your DB name
    alter role db_datareader 
    Add member alias -- Type USER name from step 2
    -- You can use any Database Role which exists 
    -- (examples: db_owner, db_datareader, db_datawriter)
    -- Replace alias with alias of the user you would like to give access and domain with the company domain you are using.
    ```
## Server level permission

1. To grant full access to a user to all serverless SQL pool databases, follow the step in this example:
    ```
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
    ```
## Transform data in a pipeline
By using the CREATE EXTERNAL TABLE AS statement, you can use Azure Synapse serverless SQL pool to transform data as part of a data ingestion pipeline or an extract, transform, and load (ETL) process. The transformed data is persisted in files in the data lake with a relational table based on the file location; enabling you to work with the transformed data using SQL in the serverless SQL database, or directly in the file data lake.

Encapsulating a `CREATE EXTERNAL TABLE AS SELECT` (CETAS) statement in a stored procedure makes it easier for you to operationalize data transformations that you may need to *perform repeatedly*. In Azure Synapse Analytics and Azure Data Factory, you can create pipelines that connect to _linked services_, including Azure Data Lake Store Gen2 storage accounts that host data lake files, and serverless SQL pools; enabling you to call your stored procedures as part of an overall data extract, transform, and load (ETL) pipeline.

For example, you can create a pipeline that includes the following activities:

- A **Delete** activity that deletes the target folder for the transformed data in the data lake if it already exists.
- A **Stored procedure** activity that connects to your serverless SQL pool and runs the stored procedure that encapsulates your CETAS operation.

![[Pasted image 20240830192309.png]]
Creating a pipeline for the data transformation enables you to schedule the operation to run at specific times or based on specific events (such as new files being added to the source storage location).

For more information about using the **Stored procedure** activity in a pipeline, see [Transform data by using the SQL Server Stored Procedure activity in Azure Data Factory or Synapse Analytics](https://learn.microsoft.com/en-us/azure/data-factory/transform-data-using-stored-procedure) in the Azure Data Factory documentation.
## Encapsulate data transformations in a stored procedure
While you can run a `CREATE EXTERNAL TABLE AS SELECT` (CETAS) statement in a script whenever you need to transform data, it's good practice to encapsulate the transformation operation in stored procedure. This approach can make it easier to operationalize data transformations by enabling you to supply parameters, retrieve outputs, and include additional logic in a single procedure call.

For example, the following code creates a stored procedure that drops the external table if it already exists before recreating it with order data for the specified year:

```
CREATE PROCEDURE usp_special_orders_by_year @order_year INT
AS
BEGIN

	-- Drop the table if it already exists
	IF EXISTS (
                SELECT * FROM sys.external_tables
                WHERE name = 'SpecialOrders'
            )
        DROP EXTERNAL TABLE SpecialOrders

	-- Create external table with special orders
	-- from the specified year
	CREATE EXTERNAL TABLE SpecialOrders
		WITH (
			LOCATION = 'special_orders/',
			DATA_SOURCE = files,
			FILE_FORMAT = ParquetFormat
		)
	AS
	SELECT OrderID, CustomerName, OrderTotal
	FROM
		OPENROWSET(
			BULK 'sales_orders/*.csv',
			DATA_SOURCE = 'files',
			FORMAT = 'CSV',
			PARSER_VERSION = '2.0',
			HEADER_ROW = TRUE
		) AS source_data
	WHERE OrderType = 'Special Order'
	AND YEAR(OrderDate) = @order_year
END
```
***As discussed previously, dropping an existing external table does not delete the folder containing its data files. You must explicitly delete the target folder if it exists before running the stored procedure, or an error will occur.***

### Reduces client to server network traffic

The commands in a procedure are executed as a single batch of code; which can significantly reduce network traffic between the server and client because only the call to execute the procedure is sent across the network.

### Provides a security boundary

Multiple users and client programs can perform operations on underlying database objects through a procedure, even if the users and programs don't have direct permissions on those underlying objects. The procedure controls what processes and activities are performed and protects the underlying database objects; eliminating the requirement to grant permissions at the individual object level and simplifies the security layers.

### Eases maintenance

Any changes in the logic or file system locations involved in the data transformation can be applied only to the stored procedure; without requiring updates to client applications or other calling functions.

### Improved performance

Stored procedures are compiled the first time they're executed, and the resulting execution plan is held in the cache and reused on subsequent runs of the same stored procedure. As a result, it takes less time to process the procedure.
## Transform data files with the CREATE EXTERNAL TABLE AS SELECT statement

You can use a CREATE EXTERNAL TABLE AS SELECT (CETAS) statement in a dedicated SQL pool or serverless SQL pool to persist the results of a query in an external table, which stores its data in a file in the data lake.

The CETAS statement includes a SELECT statement that queries and manipulates data from any valid data source (which could be an existing table or view in a database, or an OPENROWSET function that reads file-based data from the data lake). The results of the SELECT statement are then persisted in an external table, which is a metadata object in a database that provides a relational abstraction over data stored in files. The following diagram illustrates the concept visually:

By applying this technique, you can use SQL to extract and transform data from files or tables, and store the transformed results for downstream processing or analysis. Subsequent operations on the transformed data can be performed against the relational table in the SQL pool database or directly against the underlying data files.
![[Pasted image 20240830170607.png]]

### Creating external database objects to support CETAS

To use CETAS expressions, you must create the following types of object in a database for either a serverless or dedicated SQL pool. When using a serverless SQL pool, create these objects in a custom database (created using the `CREATE DATABASE` statement), not the **built-in** database.

### External data source

An external data source encapsulates a connection to a file system location in a data lake. You can then use this connection to specify a relative path in which the data files for the external table created by the CETAS statement are saved.

If the source data for the CETAS statement is in files in the same data lake path, you can use the same external data source in the OPENROWSET function used to query it. Alternatively, you can create a separate external data source for the source files or use a fully qualified file path in the OPENROWSET function.

To create an external data source, use the `CREATE EXTERNAL DATA SOURCE` statement, as shown in this example:
```
-- Create an external data source for the Azure storage account
CREATE EXTERNAL DATA SOURCE files
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/files/',
    TYPE = HADOOP, -- For dedicated SQL pool
    -- TYPE = BLOB_STORAGE, -- For serverless SQL pool
    CREDENTIAL = storageCred
);
```

The previous example assumes that users running queries that use the external data source will have sufficient permissions to access the files. An alternative approach is to encapsulate a credential in the external data source so that it can be used to access file data without granting all users permissions to read it directly:

```
CREATE DATABASE SCOPED CREDENTIAL storagekeycred
WITH
    IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=xxx...';

CREATE EXTERNAL DATA SOURCE secureFiles
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/secureFiles/'
    CREDENTIAL = storagekeycred
);
```

In addition to SAS authentication, you can define credentials that use _managed identity_ (the Microsoft Entra identity used by your Azure Synapse workspace), a specific Microsoft Entra principal, or passthrough authentication based on the identity of the user running the query (which is the default type of authentication). To learn more about using credentials in a serverless SQL pool, see the [Control storage account access for serverless SQL pool in Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-storage-files-storage-access-control) article in Azure Synapse Analytics documentation.

### External file format

The CETAS statement creates a table with its data stored in files. You must specify the format of the files you want to create as an external file format.

To create an external file format, use the `CREATE EXTERNAL FILE FORMAT` statement, as shown in this example:

```
CREATE EXTERNAL FILE FORMAT ParquetFormat
WITH (
        FORMAT_TYPE = PARQUET,
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
    );
```

### Using the CETAS statement

After creating an external data source and external file format, you can use the CETAS statement to transform data and stored the results in an external table.

For example, suppose the source data you want to transform consists of sales orders in comma-delimited text files that are stored in a folder in a data lake. You want to filter the data to include only orders that are marked as "special order", and save the transformed data as Parquet files in a different folder in the same data lake. You could use the same external data source for both the source and destination folders as shown in this example:

```
CREATE EXTERNAL TABLE SpecialOrders
    WITH (
        -- details for storing results
        LOCATION = 'special_orders/',
        DATA_SOURCE = files,
        FILE_FORMAT = ParquetFormat
    )
AS
SELECT OrderID, CustomerName, OrderTotal
FROM
    OPENROWSET(
        -- details for reading source files
        BULK 'sales_orders/*.csv',
        DATA_SOURCE = 'files',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        HEADER_ROW = TRUE
    ) AS source_data
WHERE OrderType = 'Special Order';
```

*The `LOCATION` and `BULK` parameters in the previous example are relative paths for the results and source files respectively. The paths are relative to the file system location referenced by the **files** external data source.

An **important** point to understand is that you _**must**_ use an external data source to specify the location where the transformed data for the external table is to be saved. When file-based source data is stored in the same folder hierarchy, you can use the same external data source. Otherwise, you can use a second data source to define a connection to the source data or use the fully qualified path, as shown in this example:

```
CREATE EXTERNAL TABLE SpecialOrders
    WITH (
        -- details for storing results
        LOCATION = 'special_orders/',
        DATA_SOURCE = files,
        FILE_FORMAT = ParquetFormat
    )
AS
SELECT OrderID, CustomerName, OrderTotal
FROM
    OPENROWSET(
        -- details for reading source files
        BULK 'https://mystorage.blob.core.windows.net/data/sales_orders/*.csv',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        HEADER_ROW = TRUE
    ) AS source_data
WHERE OrderType = 'Special Order';
```

### Dropping external tables

If you no longer need the external table containing the transformed data, you can drop it from the database by using the `DROP EXTERNAL TABLE` statement, as shown here:
`DROP EXTERNAL TABLE SpecialOrders;`
## Creating an external data source
You can use the OPENROWSET function with a BULK path to query file data from your own database, just as you can in the **master** database; but if you plan to query data in the same location frequently, it's more efficient to define an external data source that references that location. For example, the following code creates a data source named _files_ for the hypothetical `https://mydatalake.blob.core.windows.net/data/files/` folder:
```
CREATE EXTERNAL DATA SOURCE files
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/files/'
)
```
One benefit of an external data source, is that you can simplify an OPENROWSET query to use the combination of the data source and the relative path to the folders or files you want to query:
```
SELECT *
FROM
    OPENROWSET(
        BULK 'orders/*.csv',
        DATA_SOURCE = 'files',
        FORMAT = 'csv',
        PARSER_VERSION = '2.0'
    ) AS orders
```
In this example, the **BULK** parameter is used to specify the relative path for all .csv files in the **orders** folder, which is a subfolder of the **files** folder referenced by the data source.

Another benefit of using a data source is that you can assign a credential for the data source to use when accessing the underlying storage, enabling you to provide access to data through SQL without permitting users to access the data directly in the storage account. For example, the following code creates a credential that uses a shared access signature (SAS) to authenticate against the underlying Azure storage account hosting the data lake.

```
CREATE DATABASE SCOPED CREDENTIAL sqlcred
WITH
    IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=xxx...';
GO

CREATE EXTERNAL DATA SOURCE secureFiles
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/secureFiles/'
    CREDENTIAL = sqlcred
);
GO
```


In addition to SAS authentication, you can define credentials that use _managed identity_ (the Microsoft Entra identity used by your Azure Synapse workspace), a specific Microsoft Entra principal, or passthrough authentication based on the identity of the user running the query (which is the default type of authentication). To learn more about using credentials in a serverless SQL pool, see the [Control storage account access for serverless SQL pool in Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-storage-files-storage-access-control) article in Azure Synapse Analytics documentation.
## Creating a database
You can create a database in a serverless SQL pool just as you would in a SQL Server instance. You can use the graphical interface in Synapse Studio, or a CREATE DATABASE statement. One consideration is to set the collation of your database so that it supports conversion of text data in files to appropriate Transact-SQL data types.

The following example code creates a database named salesDB with a collation that makes it easier to import UTF-8 encoded text data into VARCHAR columns.
`CREATE DATABASE SalesDB COLLATE Latin1_General_100_BIN2_UTF8


## Query files using a serveless SQL pool
You can use a serverless SQL pool to query data files in various common file formats, including:
- Delimited text, such as comma-separated values (CSV) files.
- JavaScript object notation (JSON) files.
- Parquet files.
	- These are efficient for big data. Think of them as pre-optimized data files that SQL can read easily.
## OPENROWSET
#OPENROWSET 
The basic syntax for querying is the same for all of these types of file, and is built on the OPENROWSET SQL function; which generates a tabular rowset from data in one or more files.

You can use the OPENROWSET function in SQL queries that run in the default **master** database of the built-in serverless SQL pool to explore data in the data lake. However, sometimes you may want to create a custom database that contains some objects that make it easier to work with external data in the data lake that you need to query frequently.

The OPENROWSET function includes more parameters that determine factors such as:
- The schema of the resulting rowset
- Additional formatting options for delimited text files.
full syntax for the OPENROWSET function in the [Azure Synapse Analytics documentation](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-openrowset#syntax) 
- The output from OPENROWSET is a rowset to which an alias must be assigned. In the previous example, the alias **rows** is used to name the resulting rowset.

The following query could be used to extract data from CSV files.
SQL:
```
SELECT TOP 100 *
FROM OPENROWSET(
    BULK 'https://mydatalake.blob.core.windows.net/data/files/*.csv',
    FORMAT = 'csv') AS rows
```

## Working with JSON
Just like in JavaScript, you can extract values from JSON using dot notation. In SQL, it's done with a function:
- `SELECT JSON_VALUE(doc, '$.product_name') AS product


## Bulk Parameters
The **BULK** parameter includes the **FULL URL** to the location in the data lake containing the data files. This can be an individual file, or a folder with a wildcard expression to filter the file types that should be included. The **FORMAT** parameter specifies the type of data being queried. The example above reads delimited text from all .csv files in the **files** folder.

 Note

This example assumes that the user has access to the files in the underlying store, If the files are protected with a SAS key or custom identity, you would need to [create a server-scoped credential](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/develop-storage-files-storage-access-control?tabs=shared-access-signature#server-scoped-credential).

As seen in the previous example, you can use wildcards in the **BULK** parameter to include or exclude files in the query. The following list shows a few examples of how this can be used:

- `https://mydatalake.blob.core.windows.net/data/files/file1.csv`: Only include _file1.csv_ in the _files_ folder.
- `https://mydatalake.blob.core.windows.net/data/files/file*.csv`: All .csv files in the _files_ folder with names that start with "file".
- `https://mydatalake.blob.core.windows.net/data/files/*`: All files in the _files_ folder.
- `https://mydatalake.blob.core.windows.net/data/files/**`: All files in the _files_ folder, and recursively its subfolders.

You can also specify multiple file paths in the **BULK** parameter, separating each path with a comma.

## Querying #Parquet files
Parquet is a commonly used format for big data processing on distributed file storage. It's an efficient data format that is optimized for compression and analytical querying.

In most cases, the schema of the data is embedded within the Parquet file, so you only need to specify the **BULK** parameter with a path to the file(s) you want to read, and a **FORMAT** parameter of _parquet_; like this:

```
SELECT TOP 100 *
FROM OPENROWSET(
    BULK 'https://mydatalake.blob.core.windows.net/data/files/*.*',
    FORMAT = 'parquet') AS rows
```

## Querying partitioned data
It's common in a data lake to partition data by splitting across multiple files in subfolders that reflect partitioning criteria. This enables distributed processing systems to work in parallel on multiple partitions of the data, or to easily eliminate data reads from specific folders based on filtering criteria. For example, suppose you need to efficiently process sales order data, and often need to filter based on the year and month in which orders were placed. You could partition the data using folders, like this:

- /orders
    - /year=2020
        - /month=1
            - /01012020.parquet
            - /02012020.parquet
            - ...
        - /month=2
            - /01022020.parquet
            - /02022020.parquet
            - ...
        - ...
    - /year=2021
        - /month=1
            - /01012021.parquet
            - /02012021.parquet
            - ...
        - ...

To create a query that filters the results to include only the orders for January and February 2020, you could use the following code:
```
SELECT * FROM OPENROWSET( BULK 'https://mydatalake.blob.core.windows.net/data/orders/year=*/month=*/*.*', FORMAT = 'parquet') AS orders WHERE orders.filepath(1) = '2020' AND orders.filepath(2) IN ('1','2');
```
The numbered filepath parameters in the WHERE clause reference the wildcards in the folder names in the BULK path -so the parameter 1 is the * in the _year=*_ folder name, and parameter 2 is the * in the _month=*_ folder name.


