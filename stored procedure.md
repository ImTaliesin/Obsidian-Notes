![[Pasted image 20240926185106.png]]Using a #CETAS statement will remove all file partitions. Use stored procedures to use a #CETAS statement for every folder.

We will need to
* Create a Stored Procedure - use #CETAS to transform the data
*  Create a Stored Procedure - DROP External Tables
* Execute store procedure for each partition
* Create a view with the partitioned columns

