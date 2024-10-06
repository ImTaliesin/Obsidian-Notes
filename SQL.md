SQL includes many features and functions that enable you to manipulate data. For example, you can use SQL to:

- Filter rows and columns in a dataset.
- Rename data fields and convert between data types.
- Calculate derived data fields.
- Manipulate string values.
- Group and aggregate data.
## SQL Vocab
### OPENROWSET
OPENROWSET allows reading remote files without loading them into tables or creating external tables. Here are the key points:
- Enables reading files from Azure Storage
- Returns file contents as a set of rows with columns
- Can be used in the FROM clause of a SELECT statement like a table or view
- Supports reading files in [[CSV]], [[Parquet]], and [[Delta]] formats
#### Syntax

The basic syntax for OPENROWSET includes two mandatory parameters:

1. BULK: Specifies the URL of the file or folder in Azure storage
2. FORMAT: Indicates the file format (CSV, Parquet, or Delta)

#### Example:

```
SELECT * FROM OPENROWSET(
BULK 'https://storage_account.blob.core.windows.net/container/file.csv',  FORMAT = 'CSV' ) 
AS [result]
```
#### Additional Parameters
For CSV files, optional parameters can be specified, such as:
- Delimiter
- Whether the file contains a header row
### SELECT
- Used to retrieve data from one or more tables
- Basic syntax: `SELECT column1, column2, ... FROM table_name;`
- To select all columns: `SELECT * FROM table_name;`

### DISTINCT
- Used with SELECT to return only unique values
- Syntax: `SELECT DISTINCT column1, column2, ... FROM table_name;`

Example:
```sql
SELECT DISTINCT city FROM customers;
```
### LEAD

LEAD accesses data from subsequent rows in the same result set.

Syntax:
```sql
LEAD(column, offset, default_value) OVER (ORDER BY column)
```

Example query:
```sql
SELECT 
    sale_date,
    sale_amount,
    LEAD(sale_amount, 1, 0) OVER (ORDER BY sale_date) AS next_sale_amount,
    sale_amount - LEAD(sale_amount, 1, 0) OVER (ORDER BY sale_date) AS sale_difference
FROM 
    sales_table
ORDER BY 
    sale_date;
```

Sample output:
```
sale_date   | sale_amount | next_sale_amount | sale_difference
------------|-------------|-------------------|----------------
2023-01-01  | 100         | 150               | -50
2023-01-02  | 150         | 120               | 30
2023-01-03  | 120         | 200               | -80
2023-01-04  | 200         | 180               | 20
2023-01-05  | 180         | 0                 | 180
```

### LAG

LAG accesses data from previous rows in the result set.

Syntax:
```sql
LAG(column, offset, default_value) OVER (ORDER BY column)
```

Example query:
```sql
SELECT 
    month,
    revenue,
    LAG(revenue, 1, 0) OVER (ORDER BY month) AS previous_month_revenue,
    (revenue - LAG(revenue, 1, 0) OVER (ORDER BY month)) / LAG(revenue, 1, 0) OVER (ORDER BY month) * 100 AS growth_rate
FROM 
    monthly_revenue
ORDER BY 
    month;
```

Sample output:
```
month     | revenue | previous_month_revenue | growth_rate
----------|---------|------------------------|------------
2023-01   | 10000   | 0                      | NULL
2023-02   | 12000   | 10000                  | 20.00
2023-03   | 11500   | 12000                  | -4.17
2023-04   | 13000   | 11500                  | 13.04
2023-05   | 14500   | 13000                  | 11.54
```

### PARTITION BY

PARTITION BY divides the result set into partitions for window functions.
Let's use a scenario where we want to calculate the average salary for each department, as well as each employee's salary difference from their department average.

Here's the SQL query using PARTITION BY:

```sql
SELECT 
    employee_name,
    department,
    salary,
    AVG(salary) OVER (PARTITION BY department) AS dept_avg_salary,
    salary - AVG(salary) OVER (PARTITION BY department) AS salary_diff_from_avg
FROM 
    employees
ORDER BY 
    department, salary DESC;
```
sample output:
```
employee_name | department | salary | dept_avg_salary | salary_diff_from_avg
--------------|------------|--------|-----------------|----------------------
John Smith    | HR         | 75000  | 66666.67        | 8333.33
Jane Doe      | HR         | 68000  | 66666.67        | 1333.33
Mike Johnson  | HR         | 57000  | 66666.67        | -9666.67
Sarah Lee     | IT         | 92000  | 81000.00        | 11000.00
Tom Brown     | IT         | 85000  | 81000.00        | 4000.00
Lisa Wang     | IT         | 78000  | 81000.00        | -3000.00
David Chen    | IT         | 69000  | 81000.00        | -12000.00
Emily White   | Sales      | 88000  | 79000.00        | 9000.00
Chris Green   | Sales      | 82000  | 79000.00        | 3000.00
Alex Taylor   | Sales      | 67000  | 79000.00        | -12000.00
```
### OVER

OVER defines the window of rows for a window function to operate on.

Example combining PARTITION BY and OVER:
```sql
SELECT 
    employee_name,
    department,
    salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS salary_rank
FROM 
    employees
ORDER BY 
    department, salary_rank;
```

Sample output:
```
employee_name | department | salary | salary_rank
--------------|------------|--------|------------
John Doe      | IT         | 85000  | 1
Jane Smith    | IT         | 82000  | 2
Bob Johnson   | IT         | 78000  | 3
Alice Brown   | HR         | 75000  | 1
Carol White   | HR         | 72000  | 2
David Lee     | HR         | 70000  | 3
Eva Green     | Sales      | 90000  | 1
Frank Black   | Sales      | 88000  | 2
Grace Tan     | Sales      | 85000  | 3
```
### UNION
returns the result of subquery1 and the rows of subquery 2
```sql
(select * from jc_bike_data order by started_at asc limit 5)
union
(select * from jc_bike_data order by started_at desc limit 5)
```

### intersect 
returns the result of subquery1 that overlap with the rows of subquery 2
```sql
(select * from jc_bike_data where started_at between '2022-01-01 00:10:00.000' and '2022-01-01 00:12:00.000')
intersect
(select * from jc_bike_data where started_at between '2022-01-01 00:11:00.000' and '2022-01-01 00:19:00.000')
```

except
returns the result of subquery1 which are not in subquery 2
```sql
(select * from jc_bike_data where started_at between '2022-01-01 00:10:00.000' and '2022-01-01 00:12:00.000')
except
(select * from jc_bike_data where started_at between '2022-01-01 00:11:00.000' and '2022-01-01 00:19:00.000')
```
### WHERE
- Filters data based on specified conditions
- Syntax: `SELECT column1, column2, ... FROM table_name WHERE condition;`

Example:
```sql
SELECT * FROM jc_bike_data WHERE start_station_id = 'HB103';
```

### IN
- Specifies multiple values in a WHERE clause
- Syntax: `WHERE column_name IN (value1, value2, ...);`

Example:
```sql
SELECT * FROM jc_bike_data WHERE start_station_id IN ('jc023', 'jc019', 'jc093');
```

### LIKE
- Used for pattern matching in a WHERE clause
- `%` represents zero, one, or multiple characters
- `_` represents a single character

Example:
```sql
SELECT * FROM station_data WHERE station_name LIKE '%St%';
```

### DELETE FROM
- Removes rows from a table that match specified conditions
- If no condition is specified, all rows will be deleted
- Syntax: `DELETE FROM table_name WHERE condition;`

Example:
```sql
DELETE FROM temp_station_data WHERE station_id = 'HB203';
```

### Catalog
- Specifies the database
- Can be referenced using `catalog.schema.table`
- Or by using `USE catalog catalog_name;`

Examples:
```sql
SELECT * FROM samples.nyctaxi.trips;

USE catalog dbw_sql_udemytutorial;
```

### CREATE
- Used to create new tables
- Can copy data from an existing table

Example:
```sql
CREATE TABLE test_2 AS SELECT * FROM test_1;
```

### CAST
- Converts data from one type to another
- Syntax: `CAST(expression AS data_type)`

Examples:
```sql
SELECT CAST('2023-09-12' AS DATE) AS converted_date;
SELECT CAST(12345 AS VARCHAR(10)) AS number_as_string;
SELECT CAST(GETDATE() AS VARCHAR(23)) AS date_as_string;
```

#### TRUNCATE
- Deletes all rows from a table but keeps the table structure
- Syntax: `TRUNCATE TABLE table_name;`

#### ALTER
- Modifies an existing table structure
- Can rename tables, add/drop columns, etc.

Examples:
```sql
ALTER TABLE old_table_name RENAME TO new_table_name;
ALTER TABLE table_name ADD COLUMN new_column data_type;
ALTER TABLE table_name DROP COLUMN column_name;
```

#### IDENTITY
- Used for auto-incrementing columns in SQL Server
- Automatically generates unique, incrementing values

Example:
```sql
CREATE TABLE example_table (
    id INT IDENTITY(1,1) PRIMARY KEY,
    name VARCHAR(50)
);
```

### CASE
- Used for conditional processing in SQL
- Similar to if-then-else statements in other programming languages
- Can be used in SELECT, WHERE, and ORDER BY clauses

Syntax:
```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE result
END
```

Examples:

1. Simple CASE statement:
```sql
SELECT 
    order_id,
    order_total,
    CASE
        WHEN order_total < 50 THEN 'Small Order'
        WHEN order_total >= 50 AND order_total < 100 THEN 'Medium Order'
        ELSE 'Large Order'
    END AS order_size
FROM orders;
```

2. CASE in WHERE clause:
```sql
SELECT *
FROM employees
WHERE
    CASE
        WHEN department = 'Sales' THEN salary > 50000
        WHEN department = 'IT' THEN salary > 60000
        ELSE salary > 40000
    END;
```

3. CASE with aggregate functions:
```sql
SELECT 
    department,
    AVG(CASE WHEN gender = 'Male' THEN salary END) AS avg_male_salary,
    AVG(CASE WHEN gender = 'Female' THEN salary END) AS avg_female_salary
FROM employees
GROUP BY department;
```
4. CASE - create colum with varied entries with a case/if/switch statement:
```sql
SELECT 
ride_id,
trip_duration,
CASE WHEN trip_duration > 60 then 'Long'
	WHEN trip_duration >30 then 'Medium'
	ELSE 'Short'
	end as trip_category
from trip_duration; 
```


Remember that the CASE statement returns the result of the first condition that evaluates to true. If no conditions are true and there's no ELSE clause, it returns NULL.## Built-in Functions
### String Functions
* Upper() takes a string and turns all letters in it uppercase. 
* Lower() takes a string and turns all letters in it lowercase. 
* initcap() takes a string and turns the first letter uppercase and the remaining letters lowercase
* length() returns the length of a string (includes whitespace)
* right/left(table,number) returns the rightmost / leftmost length using your desired length set inside the parenthesis
* CONCAT( returns the concatenation/combination of the columns
	* `select concat(start_station_id, '-', end_station_id) as start_and_stop from jc_bike_data_22 `
### Numerical Functions
* + add
* * multiply
* / divide
* round(columsmath, 1) takes your math for your first arg and an int as the second arg. The second arg determines how many decimal points you round to. 

#### Date functions
Say you want to create a table that shows how much time elapsed. If you have a start and end time you can do this:
`DATEDIFF(interval, start_date, end_date)`
`create table trip_duration as select ride_id, datediff(MINUTE, started_at, ended_at) as trip_duration from jc_bike_data_22`

#### GROUP BY
allows you to group rows based on the values in one or more colums. It is used in conjunction with aggregate functions to perform calculations on those groups.

Syntax: 
```sql
SELECT non_aggregated_column(s), aggregated_column(s)
FROM table_bame
WHERE condition --optional
GROUP BY non_aggregated_columns(s);
```

`SELECT ride_id, count(trip_duration) from trip_duration group by ride_id` 
^ this will show the ride id column and another column that shows how many trip_durations each trip has (it is 1).

```sql
SELECT department, job_title, AVG(salary) as avg_salary
FROM employees
GROUP BY department, job_title;
```

Here's what happens step by step:

1. **Grouping Rows**:
   - SQL first groups the rows based on unique combinations of the values in the GROUP BY columns (department and job_title).
   - Each unique combination forms a group.

2. **Selecting Columns**:
   - For each group, SQL selects the values of department and job_title. These will be the same for all rows in the group.

3. **Aggregating**:
   - SQL then applies the aggregate function (AVG in this case) to the specified column (salary) for each group.

4. **Result Creation**:
   - SQL creates one output row for each group, containing:
     - The group's department
     - The group's job_title
     - The calculated average salary for that group
### Visualization:

Original Data:
```
| department | job_title | salary |
|------------|-----------|--------|
| Sales      | Manager   | 70000  |
| Sales      | Rep       | 50000  |
| Sales      | Rep       | 55000  |
| IT         | Manager   | 80000  |
| IT         | Developer | 65000  |
| IT         | Developer | 60000  |
```

Grouping Process:
1. Group (Sales, Manager): [70000]
2. Group (Sales, Rep): [50000, 55000]
3. Group (IT, Manager): [80000]
4. Group (IT, Developer): [65000, 60000]

Result After Aggregation:
```
| department | job_title | avg_salary |
|------------|-----------|------------|
| Sales      | Manager   | 70000      |
| Sales      | Rep       | 52500      |
| IT         | Manager   | 80000      |
| IT         | Developer | 62500      |
```

Each row in the result represents a group from the original data, with the average salary calculated for that group.

### HAVING 

The HAVING clause is used in conjunction with GROUP BY to filter groups based on aggregated results. It's necessary because WHERE cannot be used with aggregate functions.

#### Key Points:

1. HAVING is used to filter groups, whereas WHERE filters individual rows.
2. HAVING is applied after GROUP BY, while WHERE is applied before GROUP BY.
3. HAVING can use aggregate functions, but WHERE cannot.

#### Syntax:
`SELECT column1, column2, AGGREGATE_FUNCTION(column3) FROM table_name GROUP BY column1, column2 HAVING condition;`
#### Examples:
1. Find departments with average salary above 60000:
`SELECT department, AVG(salary) as avg_salary FROM employees GROUP BY department HAVING AVG(salary) > 60000;`

2. Select product categories with more than 100 total sales:
`SELECT category, SUM(sales) as total_sales FROM products GROUP BY category HAVING SUM(sales) > 100;`

3. Combining WHERE and HAVING:
`SELECT department, job_title, AVG(salary) as avg_salary FROM employees WHERE hire_date > '2020-01-01' GROUP BY department, job_title HAVING AVG(salary) > 50000;`

This query first filters for employees hired after Jan 1, 2020 (using WHERE), then groups by department and job title, and finally filters for groups with an average salary above 50000 (using HAVING).

#### Why HAVING is Necessary:

- WHERE filters rows before they are grouped and aggregated.
- HAVING filters groups after aggregation.
- You cannot use aggregate functions in a WHERE clause.

Incorrect (will cause an error):
`SELECT department, AVG(salary) as avg_salary FROM employees WHERE AVG(salary) > 60000  -- This will cause an error GROUP BY department;`

Correct:
`SELECT department, AVG(salary) as avg_salary FROM employees GROUP BY department HAVING AVG(salary) > 60000;`

#### Remember:
- Use WHERE for filtering individual rows based on column values.
- Use HAVING for filtering groups based on the results of aggregate functions.
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



## Check for duplicate entries

## Explore JSON using OPENJSON
- `FIELDTERMINATOR = '0x0b',`: Sets the field delimiter (vertical tab in this case).
- `FIELDQUOTE = '0x0b',`: Sets the field quote character (also vertical tab).
- `ROWTERMINATOR = '0x0a'`: Sets the row terminator (newline character).
* `jsonDoc NVARCHAR(MAX)`: Defines a column named 'jsonDoc' of type NVARCHAR(MAX) to hold the entire JSON document.

* OPENJSON is a table-valued function that parses JSON text and returns objects and properties from the JSON input as rows and columns.
	* - OPENROWSET reads the JSON file into a single NVARCHAR(MAX) column.
	- CROSS APPLY is used with OPENJSON to unpack the JSON data.
	- The inner WITH clause in OPENJSON specifies the structure of the JSON data, mapping JSON properties to SQL columns.
```SQL
--View structured json data
select rate_code, rate_code_id
from OPENROWSET(
    BULK 'rate_code_multi_line.json',
    data_source = 'nyc_taxi_data_raw',
    format = 'CSV',
    PARSER_VERSION = '1.0',
    FIELDTERMINATOR = '0x0b',
    FIELDQUOTE = '0x0b',
    ROWTERMINATOR = '0x0b'
)
WITH (
    jsonDoc NVARCHAR(MAX)
) AS payment_type
CROSS APPLY OPENJSON (jsonDoc)
WITH(
    rate_code_id SMALLINT,
    rate_code VARCHAR(20)
)
```

```sql
SELECT *
	FROM OPENROWSET(
		BULK'payment_type.json',
		DATA_SOURCE = 'nyx_taxi_data_raw',
		FORMAT = 'CSV',
		PARSER_VERSION = '2.0',
		FIELDTERMINATOR = '0x0b',
		FIELDQUOTE = '0x0b',
		ROWTERMINATOR = '0x0a'
	)
	WITH (
	jsonDoc NVARCHAR(MAX)
	) as payment_type;
```

use this query to find the info from the json doc
```sql
SELECT  JSON_VALUE (jsonDOc, '$.payment_type') payment_type,
		JSON_VALUE (jsonDOc, '$.payment_type_desc') payment_typedesc,
FROM OPENROWSET(
		BULK'payment_type.json',
		DATA_SOURCE = 'nyx_taxi_data_raw',
		FORMAT = 'CSV',
		PARSER_VERSION = '2.0',
		FIELDTERMINATOR = '0x0b',
		FIELDQUOTE = '0x0b',
		ROWTERMINATOR = '0x0a'
	)
	WITH (
	jsonDoc NVARCHAR(MAX)
	) as payment_type;
```
## How to select folders/subfolders/subfiles

CREATE EXTERNAL DATA SOURCE nyc_taxi_data_raw
WITH (
    LOCATION = 'https://synapsecoursedatalakes.dfs.core.windows.net/nyc-taxi-data'
)

This statement creates an external data source named 'nyc_taxi_data_raw'.
The LOCATION parameter specifies the root URL of the data lake.
This allows you to use relative paths in subsequent OPENROWSET calls.

### Selecting Data from a Specific Folder
```
SELECT TOP 100 *
FROM OPENROWSET(
    BULK 'trip_data_green_csv/year=2020/month=01/*',
    DATA_SOURCE = 'nyc_taxi_data_raw',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE
) AS [result]
```

This query selects data from all files in the January 2020 folder.
The * wildcard in the BULK path means it will read all files in that folder.
DATA_SOURCE refers to the previously created external data source.

### Selecting Data from Multiple Subfolders
```
SELECT TOP 100 *
FROM OPENROWSET(
    BULK 'trip_data_green_csv/year=*/month=*/*.csv',
    DATA_SOURCE = 'nyc_taxi_data_raw',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE
) AS [result]
```
This query selects data from all CSV files across all years and months.
The * wildcards in 'year=' and 'month=' allow it to match any year and month subfolder.

### Counting Records in Specific Subfolders
``` sql
SELECT
result.filename() AS file_name,
count(1) as record_count
FROM OPENROWSET(
    BULK ('trip_data_green_csv/year=2020/month=01/*.csv', 'trip_data_green_csv/year=2020/month=03/*.csv'),
    DATA_SOURCE = 'nyc_taxi_data_raw',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE
) AS [result]
group by result.filename()
order by result.filename()
```
Using filepath() Function to Extract Folder Information
```sql
SELECT
    result.filename() AS file_name,
    result.filepath(1) as year,
    result.filepath(2) as month,
    count(1) as record_count
FROM OPENROWSET(
    BULK 'trip_data_green_csv/year=*/month=*/*.csv',
    DATA_SOURCE = 'nyc_taxi_data_raw',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE
) AS [result] 
WHERE result.filename() IN ('green_tripdata_2020-01.csv', 'green_tripdata_2021-01.csv')
GROUP BY result.filename(), result.filepath(1), result.filepath(2)
ORDER BY result.filename(), result.filepath(1), result.filepath(2)
```

This query demonstrates several advanced techniques for working with external data:

filepath() function:
result.filepath(1) extracts the first folder name in the path (year in this case).
result.filepath(2) extracts the second folder name in the path (month in this case).
This is useful for folder structures that represent data hierarchies.

Wildcard usage in BULK path:
'trip_data_green_csv/year=*/month=*/*.csv' allows selecting all CSV files from all year and month combinations.

filename() function:
result.filename() returns the name of each file being processed.
Used in both SELECT and WHERE clauses.

Filtering specific files:
The WHERE clause filters for specific filenames, allowing precise control over which files are processed.

Grouping and aggregation:
The GROUP BY clause includes all non-aggregated columns from the SELECT clause.
This allows for counting records per unique combination of filename, year, and month.

Ordering results:
The ORDER BY clause sorts the results by filename, year, and then month.

Key Points:
The filepath() function is powerful for extracting information from the file path itself.
Combining wildcards in the BULK path with specific filename filtering in the WHERE clause provides flexible yet precise data selection.
When using functions like filepath() in the SELECT clause, remember to include them in the GROUP BY clause as well.
This approach allows for efficient analysis of data distributed across a hierarchical folder structure.
## Views:
constructs a virtual table that has no physical data based on the result-set of a SQL query

Syntax:
`CREATE [ OR REPLACE ] VIEW [ IF NOT EXISTS ] view_name AS query; `

Example:
```sql
create view course_project.citibike.vw_bike_data as select * from course_project.citibike.jc_bike_data_22 where start_station_id = 'JC014';
```

Deleting view:
`DROP VIEW view_name`

## Create an external data source
```sql
create external data source nyc_taxi_data
with (
	LOCATION = 'abfss:nyc-taxi-data@synapseudemycoursedatalakes.dfs.core.windows.net/'
)
```
## UTF8 Characters/Collation
- COLLATE determines how string data is compared and sorted in a database.
- Strings can be stored in different ways. You can apply collation to a column in two different ways.
```
--Collate data per column
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://synapsecoursedatalakes.dfs.core.windows.net/nyc-taxi-data/raw/taxi_zone.csv',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        HEADER_ROW=TRUE
    )
    WITH (
        LocationID SMALLINT,
        Borough VARCHAR(15) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
        Zone VARCHAR(50) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
        service_zone VARCHAR(15) COLLATE Latin1_General_100_CI_AI_SC_UTF8)
    AS fields
```

```
--Collate the entire database
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://synapsecoursedatalakes.dfs.core.windows.net/nyc-taxi-data/raw/taxi_zone.csv',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        HEADER_ROW=TRUE
    )
    WITH (
        LocationID SMALLINT,
        Borough VARCHAR(15) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
        Zone VARCHAR(50) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
        service_zone VARCHAR(15) COLLATE Latin1_General_100_CI_AI_SC_UTF8)
    AS fields

CREATE DATABASE nyc_taxi_discovery;

USE nyc_taxi_discovery;

ALTER DATABASE nyc_taxi_discovery COLLATE Latin1_General_100_CI_AI_SC_UTF8;

SELECT name, collation_name FROM sys.databases;
```
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

| Permission  | File                                                            | Directory                                                       |
| ----------- | --------------------------------------------------------------- | --------------------------------------------------------------- |
| Read (R)    | Can read the contents of a file                                 | Requires Read and Execute to list the contents of the directory |
| Write (W)   | Can write or append to a file                                   | Requires Write and Execute to create child items in a directory |
| Execute (X) | Does not mean anything in the context of Data Lake Storage Gen2 | Required to traverse the child items of a directory             |
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


