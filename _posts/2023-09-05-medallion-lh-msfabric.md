---
title: Medallion Lakehouse Architecture in a MS Fabric lakehouse
date: 2023-09-05 12:00 -500
categories: [Architecture, Azure, Fabric, Data]
tags: [medallion, lakehouse, azure, microsoft-fabric]
author: gowda
---

This blog outlines steps involved in building a medallion lakehouse architecture in a microsoft fabric lakehouse using notebooks.
    * Create a Workspace
    * Create a Lakehouse
    * Upload data to the Bronze layer
    * Transform the data and load it to the Silver Delta table
    * Transform the data further and load it to the Gold Delta tables
    * Explore the dataset and create relationship in the form of Fact table and Dimension tables 

## Architecture

![Desktop View](/assets/img/msfabric-medallion-arch.png){: width="800"}

## Create a Workspace



## Create a Lakehouse

```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE' AND TABLE_SCHEMA = 'SalesLT'
```

ADF final state screenshots that shows parameters, linkedservices and calling databricks notebooks from ADF
![Desktop View](/assets/img/ADF-linkedservices.png){: width="800"}
![Desktop View](/assets/img/ADF-pipeline.png){: width="800"}
![Desktop View](/assets/img/linkedservice-blobstorage.png){: width="800"}
![Desktop View](/assets/img/linkedservice-params-blobstorage.png){: width="800"}


## References:
* <https://piethein.medium.com/using-dbt-for-building-a-medallion-lakehouse-architecture-azure-databricks-delta-dbt-31a31fc9bd0>
* <https://www.youtube.com/watch?v=KsO2FHQdILs>
* <https://anujsen02.medium.com/analytics-engineering-on-the-lakehouse-using-dbt-databricks-part-1-c4d773731ffe>
* <https://app.pluralsight.com/library/courses/building-etl-pipeline-microsoft-azure-databricks/table-of-contents>
* <https://www.youtube.com/watch?v=x3-qUw9XWMA>
