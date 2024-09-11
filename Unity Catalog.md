is a unified governance soluition for data and AI assets on the Lakehouse. Providing centralized access control auditing, lineage, and data discovery capabilities across Azure Databricks workspaces. 

It takes care of user/group management and access control. The Unity catalog can contain multiple workspaces. 

Unity Catalog
* A single place to administer data access policies that apply accross all workspaces
* Grant permissions using ANSI SQL
* Captures audit logs
* Captures data lineage
* Tag, document, and search for data assets

#Metastore: The top-level container for metadata. It represents the metadata such as the info about the objects being managed by the megastore as well as the access control lists that govern access to those assets. It can contain multiple catalogs.
#Catalogs: The first layer of the object hieracrhy. used to organize data assets. These catalogs contain Schemas.
#Schema: Also known as databases, schemas are the second layer of the object hierarchy and contain tables and views.


