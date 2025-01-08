[[Unity Catalog]] [[SQL]] [[PySpark]]

# Project Steps
## Imports

### pyspark.sql
*  A library to use sql commands in pyspark

#### pyspark.sql.functions
	current_timestamp()
		Returns current timestamp in the cluster's timezone
		* No parameters required
		* Returns timestamp type
		* Example: `df.withColumn("current_time", current_timestamp())`

	to_timestamp() 
		Converts string to timestamp using specified format 
		Takes 2 params: column/string and format pattern
		Returns timestamp type 
		Example: to_timestamp(col("date_string"), "yyyy-MM-dd HH:mm:ss")

	concat()
		Concatenates multiple columns/literals into single string
		Takes multiple params: columns/literals to combine
		Returns string type
		Example: concat(col("first_name"), lit(" "), col("last_name"))

	col()
		References column in DataFrame
		Takes 1 param: column name as string
		Returns Column type
		Example: col("column_name")

	lit()
		Creates literal/constant value as column
		Takes 1 param: value to convert
		Returns Column type with constant value
		Example: lit("constant_text")
		
#### pyspark.sql.types
	StructType()  holds multiple StructField() 
		requires fields
		Example: circuits_schema = StructType(fields= [StructField('name',StringType(), False)

	StructField()
		Has 3 params: StructField('column_name', {name}
								StringType(), {its type}
								True/False) {nullable or not}


