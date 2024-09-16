A fully managed, serverless data integration solution for ingesting, preparing and transforming all of your data at scale.

Data factories are not meant for data migrating, data streaming, or complex data transformations ([[databricks]]/spark), or data storage.
![[Pasted image 20240906143943.png]]

We will use azure data factory for all data integration and orchestration. It will run  transformations with [[HDInsight]] and Azure [[databricks]]. We have three transformation technologies being used, data flow, HDInsight, Databricks.

[[Dataflow]] gives you a codefree transformation tool which makes it easy to develop and maintain the transoformation.
- Use this for simple and medium level complexity transformations as it lacks the ability to make complex transformations
[[HDInsight]] gives you the ability to write code in a [[SQL]] like language called [[Hive]] and a scripting language called [[Pig]].
[[databricks]] requires you to write code with [[Python]] or [[SparkSQL]]

Also used is [[Azure Blob Storage]], [[Data Lake]], [[Azure Synapse]], [[Power BI]], [[Azure Databases]] [[Database]]

## Useful Links & Resources
#### Lecture: Project Overview
ECDC Website for Covid-19 Data - [https://www.ecdc.europa.eu/en/covid-19/data](https://www.ecdc.europa.eu/en/covid-19/data)

Euro Stat Website for Population Data - [https://ec.europa.eu/eurostat/estat-navtree-portlet-prod/BulkDownloadListing?file=data/tps00010.tsv.gz](https://ec.europa.eu/eurostat/estat-navtree-portlet-prod/BulkDownloadListing?file=data/tps00010.tsv.gz)
#### Lecture: Azure Storage Solutions
Introduction to Azure Storage services - [https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction)

Azure SQL Database - [https://docs.microsoft.com/en-us/azure/azure-sql/database/sql-database-paas-overview](https://docs.microsoft.com/en-us/azure/azure-sql/database/sql-database-paas-overview)

Azure Synapse Analytics - [https://docs.microsoft.com/en-us/azure/synapse-analytics/overview-what-is](https://docs.microsoft.com/en-us/azure/synapse-analytics/overview-what-is)

Azure Cosmos DB - [https://docs.microsoft.com/en-us/azure/cosmos-db/introduction](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)

Azure Data Lake Storage Gen2 - [https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction)
## 2
