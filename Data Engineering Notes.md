[[Database]] [[SQL]] [[Data Lake]] [[Apache Spark]] [[Delta Lake]] [[Data Warehouse]]
## Vocab:
### Relational Table, data lake: data stored in files, data warehouse,  data lakehouse, data pipeline, key vaults implementation with pipeline

-  _ACID_: _atomicity_ (transactions complete as a single unit of work), _consistency_ (transactions leave the database in a consistent state), _isolation_ (in-process transactions can't interfere with one another), and _durability_ (when a transaction completes, the changes it made are persisted). Delta Lake tables can be used as both _sinks_ (destinations) and _sources_ for streaming data.
- **Standard formats and interoperability**. The underlying data for Delta Lake tables is stored in Parquet format, which is commonly used in data lake ingestion pipelines. 
------------------------------------------------------------------
![[Pasted image 20240904201734.png]]
## How Azure combines everything
1. Source Systems: These are the original data sources, such as transactional databases, IoT devices, or application logs.
2. Azure Data Lake Storage:
    - Acts as a centralized repository for all your raw and processed data.
    - Stores data in its native format, which can be structured, semi-structured, or unstructured.
    - Provides a scalable and cost-effective solution for big data analytics.
3. Delta Lake (on Azure Databricks):
    - An open-source storage layer that brings ACID transactions to big data workloads.
    - Sits on top of your Data Lake Storage, providing versioning, time travel, and improved performance.
    - Often used for data processing and transformation tasks before loading into the data warehouse.
4. Azure Databricks:
    - A unified analytics platform for large-scale data processing and machine learning.
    - Used for ETL processes, data science workloads, and preparing data for analytics.
    - Can read from and write to both Data Lake Storage and Delta Lake.
5. Azure Synapse Analytics: This is Microsoft's analytics service that brings together data integration, enterprise data warehousing, and big data analytics. Within Synapse, we have:
   a. External Tables:
    - Provide a way to query data directly from the Data Lake without loading it into the data warehouse.
    - Useful for exploring raw data or querying large datasets that don't need to be fully loaded.
    
    b. Staging Tables:
    - Temporary tables used to load data from the Data Lake before final processing.
    - Allow for data validation, transformation, and error handling.
    
    c. Dimension and Fact Tables:
    - The core components of your data warehouse.
    - Dimension tables contain descriptive attributes (e.g., product details, customer information).
    - Fact tables contain measurable, quantitative data (e.g., sales transactions, inventory levels).
    
    d. Data Warehouse:
    - The final, optimized structure for analytical queries and reporting.
    - Typically uses a star or snowflake schema for efficient querying.

Data Flow:

1. Raw data from source systems is ingested into Azure Data Lake Storage.
2. This data can be processed and refined using Azure Databricks, potentially utilizing Delta Lake for ACID compliance and performance optimization.
3. Processed data is then either stored back in the Data Lake or moved directly to Azure Synapse Analytics.
4. Within Synapse, data can be accessed via external tables for initial exploration or loaded into staging tables using the COPY command.
5. Data in staging tables is then transformed and loaded into dimension and fact tables, forming the data warehouse structure.
6. The resulting data warehouse is optimized for analytical queries and business intelligence tools.

This architecture allows for a flexible, scalable approach to data management and analytics:
- The Data Lake provides a cost-effective way to store large volumes of raw data.
- Delta Lake adds reliability and performance to big data operations.
- Databricks offers powerful processing capabilities for complex transformations.
- External tables in Synapse allow for querying data without full ingestion.
- The staging-to-warehouse process in Synapse ensures data quality and optimized analytical performance.
## SQL vs Python

Use SQL when:

1. Working with structured data that is already stored in a relational format or can be easily queried using SQL syntax. SQL is particularly good for querying data from Azure Synapse serverless SQL pools or lake databases.
2. Performing simple transformations, aggregations, and filters on relatively small to medium sized datasets. SQL excels at these types of operations on structured data.
3. You need to leverage SQL-specific features like stored procedures, views, or user-defined functions.
4. Querying data directly from files in a data lake using OPENROWSET functions, especially for quick exploratory analysis.
5. Creating external tables to provide a relational abstraction over file data.
6. You want to use familiar SQL syntax and don't need the full power of a distributed computing framework.

Use Python with Apache Spark when:

1. Working with very large datasets that require distributed processing. Spark is designed for big data workloads.
2. Performing complex transformations, machine learning tasks, or advanced analytics that go beyond simple SQL operations.
3. You need to work with unstructured or semi-structured data formats like JSON or need more flexibility in data manipulation.
4. Implementing end-to-end data pipelines that involve multiple steps of data cleansing, transformation, and analysis.
5. You want to leverage Spark's ability to cache data in memory for iterative processing.
6. Utilizing Spark's rich ecosystem of libraries for data science and machine learning tasks.
7. You need more programmatic control and want to write custom logic using a full programming language.

In many data engineering scenarios, a combination of both SQL and Spark may be used. For example, you might use Spark to perform initial data cleansing and complex transformations on raw data in a data lake, then save the results in a format that can be easily queried using SQL for reporting and analysis.

The choice often depends on the specific requirements of your task, the size and format of your data, the complexity of the transformations needed, and the skills of your team.
# Stages of data processing
## There are four stages for processing big data solutions that are common to all architectures:

- **Ingest** - The ingestion phase identifies the technology and processes that are used to acquire the source data. This data can come from files, logs, and other types of unstructured data that must be put into the data lake. The technology that is used will vary depending on the frequency that the data is transferred. For example, for batch movement of data, pipelines in Azure Synapse Analytics or Azure Data Factory may be the most appropriate technology to use. For real-time ingestion of data, Apache Kafka for HDInsight or Stream Analytics may be an appropriate choice.
- **Store** - The store phase identifies where the ingested data should be placed. Azure Data Lake Storage Gen2 provides a secure and scalable storage solution that is compatible with commonly used big data processing technologies.
- **Prep and train** - The prep and train phase identifies the technologies that are used to perform data preparation and model training and scoring for machine learning solutions. Common technologies that are used in this phase are Azure Synapse Analytics, Azure Databricks, Azure HDInsight, and Azure Machine Learning.
- **Model and serve** - Finally, the model and serve phase involves the technologies that will present the data to users. These technologies can include visualization tools such as Microsoft Power BI, or analytical data stores such as Azure Synapse Analytics. Often, a combination of multiple technologies will be used depending on the business requirements.


# How data can be stored in azure

![[Pasted image 20240824122808.png]]
Operational data is generated by applications and devices and stored in Azure data storage services such as Azure SQL Database, Azure Cosmos DB, and Microsoft Dataverse. Streaming data is captured in event broker services such as Azure Event Hubs.

This operational data must be captured, ingested, and consolidated into analytical stores; from where it can be modeled and visualized in reports and dashboards. These tasks represent the core area of responsibility for the data engineer. The core Azure technologies used to implement data engineering workloads include:

- Azure Synapse Analytics
- Azure Data Lake Storage Gen2
- Azure Stream Analytics
- Azure Data Factory
- Azure Databricks
## Operational and analytical data

![Diagram representing operational and analytical data.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-operational-analytical-data.png)

_Operational_ data is usually transactional data that is generated and stored by applications, often in a relational or non-relational database. _Analytical_ data is data that has been optimized for analysis and reporting, often in a data warehouse.

#### **One of the core responsibilities of a data engineer is to design, implement, and manage solutions that integrate operational and analytical data sources or extract operational data from multiple systems, transform it into appropriate structures for analytics, and load it into an analytical data store (usually referred to as ETL solutions).**

## Streaming data

![Diagram representing streaming data.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-stream-data.png)

Streaming data refers to perpetual sources of data that generate data values in real-time, often relating to specific events. Common sources of streaming data include internet-of-things (IoT) devices and social media feeds.

#### Data engineers often need to implement solutions that capture real-time stream of data and ingest them into analytical data systems, often combining the real-time data with other application data that is processed in batches.

## Data pipelines

![Diagram representing a data pipeline.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-data-pipeline.png)

Data pipelines are used to orchestrate activities that transfer and transform data. Pipelines are the primary way in which data engineers implement repeatable extract, transform, and load (ETL) solutions that can be triggered based on a schedule or in response to events.

## Data lakes

![Diagram representing a data lake.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-data-lake.png)
##### Organizations can store structured, semi-structured, and unstructured files in the data lake and then consume them from there in big data processing technologies, such as Apache Spark.

##### Whenever planning for a data lake, a data engineer should give thoughtful consideration to structure, data governance, and security. This should include consideration of factors that can influence lake structure and organization, such as:

- Types of data to be stored
- How the data will be transformed
- Who should access the data
- What are the typical access patterns
##### If you want to store data _without performing analysis on the data_, set the **Hierarchical Namespace** option to **Disabled** to set up the storage account as an Azure Blob storage account.

A data lake is a storage repository that holds large amounts of data in native, raw formats. Data lake stores are optimized for scaling to massive volumes (terabytes or petabytes) of data. The data typically comes from multiple heterogeneous sources, and may be structured, semi-structured, or unstructured.

The idea with a data lake is to store everything in its original, untransformed state. This approach differs from a traditional data warehouse, which transforms and processes the data at the time of ingestion.

## Data warehouses

![Diagram representing a data Warehouse.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-data-warehouse.png)

A data warehouse is a centralized repository of integrated data from one or more disparate sources. Data warehouses store current and historical data in relational tables that are organized into a schema that optimizes performance for analytical queries.

Data engineers are responsible for designing and implementing relational data warehouses, and managing regular data loads into tables.

## Apache Spark

![Diagram representing an Apache Spark cluster.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/4-apache-spark.png)

Apache Spark is a parallel processing framework that takes advantage of in-memory processing and a distributed file storage. It's a common open-source software (OSS) tool for big data scenarios.

#### Data engineers need to be proficient with Spark, using notebooks and other code artifacts to process data in a data lake and prepare it for modeling and analysis.
# Data Engineering w/ Azure
![Diagram of the flow of a typical enterprise data analytics solution.](https://learn.microsoft.com/en-us/training/wwl-data-ai/introduction-to-data-engineering-azure/media/3-data-engineering-azure.png)

Microsoft Azure includes many services that can be used to implement and manage data engineering workloads.

The diagram displays the flow from left to right of a typical enterprise data analytics solution, including some of the key Azure services that may be used. Operational data is generated by applications and devices and stored in Azure data storage services such as Azure SQL Database, Azure Cosmos DB, and Microsoft Dataverse. Streaming data is captured in event broker services such as Azure Event Hubs.

This operational data must be captured, ingested, and consolidated into analytical stores; from where it can be modeled and visualized in reports and dashboards. These tasks represent the core area of responsibility for the data engineer. The core Azure technologies used to implement data engineering workloads include:

- Azure Synapse Analytics
- Azure Data Lake Storage Gen2
- Azure Stream Analytics
- Azure Data Factory
- Azure Databricks

The analytical data stores that are populated with data produced by data engineering workloads support data modeling and visualization for reporting and analysis, often using visualization tools such as Microsoft Power BI.

# Azure Data Services

## Azure SQL

![Azure SQL logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-sql.png) _Azure SQL_ is the collective name for a family of relational database solutions based on the Microsoft SQL Server database engine. Specific Azure SQL services include:

- **Azure SQL Database** – a fully managed platform-as-a-service (PaaS) database hosted in Azure.
- **Azure SQL Managed Instance** – a hosted instance of SQL Server with automated maintenance, which allows more flexible configuration than Azure SQL DB but with more administrative responsibility for the owner.
- **Azure SQL VM** – a virtual machine with an installation of SQL Server, allowing maximum configurability with full management responsibility.

Database administrators typically provision and manage Azure SQL database systems to support line of business (LOB) applications that need to store transactional data.

Data engineers may use Azure SQL database systems as sources for data pipelines that perform _extract_, _transform_, and _load_ (ETL) operations to ingest the transactional data into an analytical system.

Data analysts may query Azure SQL databases directly to create reports, though in large organizations the data is generally combined with data from other sources in an analytical data store to support enterprise analytics.

## Azure Database for open-source relational databases

![Azure Database for MariaDB, MySQL, and PostreSQL logos.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-database.png) Azure includes managed services for popular open-source relational database systems, including:

- **Azure Database for MySQL** - a simple-to-use open-source database management system that is commonly used in _Linux_, _Apache_, _MySQL_, and _PHP_ (LAMP) stack apps.
    
- **Azure Database for MariaDB** - a newer database management system, created by the original developers of MySQL. The database engine has since been rewritten and optimized to improve performance. MariaDB offers compatibility with Oracle Database (another popular commercial database management system).
    
- **Azure Database for PostgreSQL** - a hybrid relational-object database. You can store data in relational tables, but a PostgreSQL database also enables you to store custom data types, with their own non-relational properties.
    

As with Azure SQL database systems, open-source relational databases are managed by database administrators to support transactional applications, and provide a data source for data engineers building pipelines for analytical solutions and data analysts creating reports.


## Azure Cosmos DB

![Azure Cosmos DB logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/cosmos-db.png) Azure Cosmos DB is a global-scale non-relational (_NoSQL_) database system that supports multiple application programming interfaces (APIs), enabling you to store and manage data as JSON documents, key-value pairs, column-families, and graphs.

In some organizations, Cosmos DB instances may be provisioned and managed by a database administrator; though often software developers manage NoSQL data storage as part of the overall application architecture. Data engineers often need to integrate Cosmos DB data sources into enterprise analytical solutions that support modeling and reporting by data analysts.

## Azure Storage

![Azure Storage logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-storage.png) Azure Storage is a core Azure service that enables you to store data in:

- **Blob containers** - scalable, cost-effective storage for binary files.
- **File shares** - network file shares such as you typically find in corporate networks.
- **Tables** - key-value storage for applications that need to read and write data values quickly.

Data engineers use Azure Storage to host _data lakes_ - blob storage with a hierarchical namespace that enables files to be organized in folders in a distributed file system.

## Azure Data Factory

![Azure Data Factory logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-data-factory.png) Azure Data Factory is an Azure service that enables you to define and schedule data pipelines to transfer and transform data. You can integrate your pipelines with other Azure services, enabling you to ingest data from cloud data stores, process the data using cloud-based compute, and persist the results in another data store.

Azure Data Factory is used by data engineers to build _extract_, _transform_, and _load_ (ETL) solutions that populate analytical data stores with data from transactional systems across the organization.

## Microsoft Fabric

![Microsoft Fabric logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/3-fabric-icon.png) Microsoft Fabric is a unified Software-as-a-Service (SaaS) analytics platform based on an open and governed lakehouse that includes functionality to support:

- Data ingestion and ETL
- Data lakehouse analytics
- Data warehouse analytics
- Data Science and machine learning
- Realtime analytics
- Data visualization
- Data governance and management
- AI-powered insights

Data engineers can use Microsoft Fabric to create a unified data analytics solution that combines data ingestion pipelines, data warehouses, real-time analytics, business intelligence, and AI-powered insights through a single service all centrally stored with Microsoft OneLake.

## Azure Databricks

![Azure Databricks logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-databricks.png) Azure Databricks is an Azure-integrated version of the popular Databricks platform, which combines the Apache Spark data processing platform with SQL database semantics and an integrated management interface to enable large-scale data analytics.

Data engineers can use existing Databricks and Spark skills to create analytical data stores in Azure Databricks.

Data Analysts can use the native notebook support in Azure Databricks to query and visualize data in an easy to use web-based interface.

## Azure Stream Analytics

![Azure Stream Analytics logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/stream-analytics.png) Azure Stream Analytics is a real-time stream processing engine that captures a stream of data from an input, applies a query to extract and manipulate data from the input stream, and writes the results to an output for analysis or further processing.

Data engineers can incorporate Azure Stream Analytics into data analytics architectures that capture streaming data for ingestion into an analytical data store or for real-time visualization.

## Azure Data Explorer

![Azure Data Explorer logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-data-explorer.png) Azure Data Explorer is a fully managed, standalone, big data analytics platform that offers high-performance querying of log and telemetry data.

Data analysts can use Azure Data Explorer to query and analyze data that includes a timestamp attribute, such as is typically found in log files and _Internet-of-things_ (IoT) telemetry data.

## Microsoft Purview

![Azure Purview logo.](https://learn.microsoft.com/en-us/training/wwl-data-ai/explore-roles-responsibilities-world-of-data/media/azure-purview.png) Microsoft Purview provides a solution for enterprise-wide data governance and discoverability. You can use Microsoft Purview to create a map of your data and track data lineage across multiple data sources and systems, enabling you to find trustworthy data for analysis and reporting.

Data engineers can use Microsoft Purview to enforce data governance across the enterprise and ensure the integrity of data used to support analytical workloads.

# How data exists in files
## Delimited text files

Data is often stored in plain text format with specific field delimiters and row terminators. The most common format for delimited data is comma-separated values (CSV). Delimited text is a good choice for structured data that needs to be accessed by a wide range of applications and services in a human-readable format.

## JavaScript Object Notation (JSON)

JSON is a ubiquitous format in which a hierarchical document schema is used to define data entities (objects) that have multiple attributes. Each attribute might be an object (or a collection of objects); making JSON a flexible format that's good for both structured and semi-structured data.

```
{
  "customers":
  [
    {
      "firstName": "Joe",
      "lastName": "Jones",
      "contact":
      [
        {
          "type": "home",
          "number": "555 123-1234"
        },
        {
          "type": "email",
          "address": "joe@litware.com"
        }
      ]
    },
    {
      "firstName": "Samir",
      "lastName": "Nadoy",
      "contact":
      [
        {
          "type": "email",
          "address": "samir@northwind.com"
        }
      ]
    }
  ]
}
```



## Extensible Markup Language (XML)

XML is a human-readable data format that was popular in the 1990s and 2000s. It's largely been superseded by the less verbose JSON format, but there are still some systems that use XML to represent data. XML uses _tags_ enclosed in angle-brackets (**<../>**) to define _elements_ and _attributes_, as shown in this example:

XMLCopy

```
<Customers>
  <Customer name="Joe" lastName="Jones">
    <ContactDetails>
      <Contact type="home" number="555 123-1234"/>
      <Contact type="email" address="joe@litware.com"/>
    </ContactDetails>
  </Customer>
  <Customer name="Samir" lastName="Nadoy">
    <ContactDetails>
      <Contact type="email" address="samir@northwind.com"/>
    </ContactDetails>
  </Customer>
</Customers>
```



## Binary Large Object (BLOB)

Ultimately, all files are stored as binary data (1's and 0's), but in the human-readable formats discussed above, the bytes of binary data are mapped to printable characters (typically through a character encoding scheme such as ASCII or Unicode). Some file formats however, particularly for unstructured data, store the data as raw binary that must be interpreted by applications and rendered. Common types of data stored as binary include images, video, audio, and application-specific documents.
