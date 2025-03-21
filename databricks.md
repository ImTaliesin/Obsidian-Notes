[[Unity Catalog]] [[SQL]] [[PySpark]] 
![[PySpark_SQL_Cheat_Sheet.pdf]]
# Auth

## Cluter-scoped auth 
	Auth is done in Databricks>Compute>Name>Advanced Settings
		spark.databricks.cluster.profile singleNode
		spark.master local[*, 4] 
		spark.databricks.delta.preview.enabled true
		fs.azure.account.key.synapsecoursedatalakes.dfs.core.windows.net
		{{secrets/udemy-storageaccount-databricks -scope/UdemyStorageAccountKey}}

# Imports
	from pyspark.sql.functions
		col
		current_timestamp()
		to_timestamp()
		concat()
		lit()
			Gives a colum a 'literal' value
				to_timestamp(concat(col('date'), lit(' '), col('time')),
				'yyyy-MM-dd HH:mm:ss'))
	from pyspark.sq1.types
		StructField
		StuctType
		StringType..etc
# Mount

# Specify Schema

# Query

# Write