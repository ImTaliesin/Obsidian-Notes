
## Imports

### Types
*  from pyspark.sql.types
	* StructField
	* StructType

`circuits_schema = StructType(fields=[StructField('circuitId', IntegerType(), False),                             column    type  nullable
                                     StructField('circuitRef', StringType(), True),
                                     StructField('name', StringType(), True),
                                     StructField('location', StringType(), True),
                                     StructField('country', StringType(), True),
                                     StructField('lat', DoubleType(), True),
                                     StructField('lng', DoubleType(), True),
                                     StructField('alt', IntegerType(), True),
                                     StructField('url', StringType(), True)
                                     
])`

### Functions


## Mounting 

### Dbutils

display(dbutils.fs.ls('mnt/udemy/processed'))![[Pasted image 20250208111704.png]]

## Partioning

