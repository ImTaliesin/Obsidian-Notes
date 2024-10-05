[[Data Engineering Notes]] [[Azure Synapse]]
![[Pasted image 20241004144819.png]]

Definitions:
1. Online Transactional Processing (OLTP): OLTP refers to systems designed to manage and process real-time transactional data. These systems are optimized for handling a large number of small, frequent transactions such as insertions, updates, and deletions.
2. Online Analytical Processing (OLAP): OLAP systems are designed for complex queries and data analysis. They focus on aggregating large volumes of historical data to support business intelligence and decision-making processes.

# Synapse Link for #CosmosDB 
![[Pasted image 20241004145101.png]]
## Architecture Components

### Azure Cosmos DB Container

- **Transactional Store**
    - Row store optimized for transactional reads and writes
    - Handles operational data
- **Analytical Store**
    - Column store optimized for analytical queries
    - Automatically synced with transactional data
### Auto Sync
- Ensures data consistency between transactional and analytical stores
### Azure Synapse Link
- Connects Cosmos DB to Azure Synapse Analytics
- Utilizes HTAP (Hybrid Transactional/Analytical Processing)

### Azure Synapse Analytics
- **Spark Pool**
    - For big data processing and machine learning
- **Serverless SQL Pool**
    - On-demand SQL query processing
- **Machine Learning** capabilities
- **Data Exploration** tools
- **BI Reporting** features

## Benefits
- Less complex solution
    - No need to create custom ETL jobs for the transactional data
    - Synapse handles the CRUD operations
- Fully managed
    - No downtime
- Near real-time analytics and reporting of operational data
- No impact to transactional system
- Automatic schema inference
- Native integration between Synapse and Cosmos DB

## Limitations
- Only SQL API and MongoDB API supported for Cosmos DB
- Dedicated SQL Pool not supported
    - Only Spark pool and serverless SQL pool accepted
- Limited support for existing Cosmos DB containers
- Backup and restore of analytical store not available

## Data Flow
1. Transactional data enters Cosmos DB
2. Auto Sync replicates data to the Analytical Store
3. Azure Synapse Link facilitates data transfer to Synapse Analytics
4. Synapse Analytics processes data for various analytical purposes

This architecture enables a seamless integration of operational and analytical data processing, providing real-time insights without complex ETL processes or impacting transactional performance.