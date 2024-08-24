[[Database]]
## Vocab:
### Relational Table, data lake, data warehouse,  data lakehouse.


# Data Engineering w/ Azure


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
