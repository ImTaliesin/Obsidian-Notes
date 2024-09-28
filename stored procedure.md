# Stored Procedures for Data Engineers

## What is a Stored Procedure?

A stored procedure is a prepared SQL code that you save so you can reuse it over and over again. Instead of writing the same SQL statements repeatedly, you can call the stored procedure.

## When and How to Use Stored Procedures as a Data Engineer

As a data engineer, you'll use stored procedures in several scenarios:

1. **Data Transformation**: For complex ETL processes.
2. **Data Consistency**: To ensure data manipulations are consistent.
3. **Performance Optimization**: Stored procedures are precompiled, often leading to faster execution.
4. **Security**: To control access to data by granting execute permissions on procedures instead of tables.
5. **Modularity**: To break complex operations into manageable, reusable units.

## Key Considerations for Stored Procedures

1. **Parameterization**: Make procedures flexible by using parameters.
2. **Error Handling**: Implement robust error handling for reliability.
3. **Transaction Management**: Use transactions for data integrity.
4. **Performance**: Consider indexing and query optimization within procedures.
5. **Maintenance**: Document procedures well for future maintenance.

## Converting the Query to a Stored Procedure

```sql
CREATE PROCEDURE sp_GetTaxiTripSummary
    @Year CHAR(4),
    @Month CHAR(2)
AS
BEGIN
    SET NOCOUNT ON;

    SELECT TOP 100 
        td.year,
        td.month, 
        tz.borough,
        cal.day_name AS trip_day,
        CASE WHEN cal.day_name IN ('Saturday', 'Sunday') THEN 'Yes' ELSE 'No' END AS trip_day_weekend_ind,
        CONVERT(DATE, td.lpep_pickup_datetime) AS trip_date,
        SUM(CASE WHEN pt.description = 'Credit card' THEN 1 ELSE 0 END) AS card_trip_count,
        SUM(CASE WHEN pt.description = 'Cash' THEN 1 ELSE 0 END) AS cash_trip_count
    FROM silver.vw_trip_data_green td
    JOIN silver.taxi_zone tz ON td.pu_location_id = tz.location_id
    JOIN silver.calendar cal ON cal.date = CONVERT(DATE, td.lpep_pickup_datetime)
    JOIN silver.payment_type pt ON td.payment_type = pt.payment_type
    WHERE td.year = @Year
        AND td.month = @Month
    GROUP BY td.year,
             td.month,
             tz.borough,
             cal.day_name,
             CONVERT(DATE, td.lpep_pickup_datetime)
    ORDER BY td.year, td.month, tz.borough, trip_date;
END
```

To execute this stored procedure:

```sql
EXEC sp_GetTaxiTripSummary @Year = '2020', @Month = '01'
```

This approach allows you to reuse the query with different year and month parameters easily.


![[Pasted image 20240926185106.png]]Using a #CETAS statement will remove all file partitions. Use stored procedures to use a #CETAS statement for every folder.

We will need to
* Create a Stored Procedure - use #CETAS to transform the data
*  Create a Stored Procedure - DROP External Tables
* Execute store procedure for each partition
* Create a view with the partitioned columns

