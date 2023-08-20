---
title: Medallion Lakehouse Architecture with Azure databricks, datafactory and DBT
date: 2023-08-20 12:00 -500
# categories: [Architecture,Azure,Data]
# tags: [medallion, akehouse, azure, dbt, datafactory,databricks, data]
author: gowda
---

This blog outlines important components and steps involved in building a medallion lakehouse architecture on Microsoft Azure.

## Components of Medallion lakehouse architecture
I will introduce and demonstrate how these tools work together with Databricks Medallion architecture.

### Azure databricks (ADB):
Databricks is popular service/tool to build data Lakehouse architecture. Databricks is built on top of popular opensource Apache Spark technology for big data processing. It is a complex process to build and manage a cluster with Apache Spark. This is where Databricks came into picture and made the entire process of creating and managing the cluster easy. Databricks on Azure utilizes the infrastructure capabilities of cloud and makes it intuitive to use. Azure powers the computation and storage layers, integrates with data and orchestration tools, provides security and monitoring capabilities.

### Azure Data factory (ADF):
Datafactory is a integration and orchestration tool on Azure cloud. It can also host traditional microsoft specific on-prem ETL tool called SSIS. It connects to various data providers and pull data into azure datalake storage before processing. It facilitates other azure tools like azure databricks, azure functions and azure LogicApps to do specific data integration and processing.

### Data Build Tool (DBT):
DBT is a transformation tool in the ETL/ELT process. There is a lot of buzz around DBT in data community. It is an open source command line tool written in Python. DBT focusses on the transformation, so it doesn’t extract or load data, but only transforms data. It is declarative and supports git review process, unit testing, monitoring and easy documentation.

### Azure data lake storage (ADLS gen2):

It is a big data storage services for big data. 2 things differentiates ADLS gen2 from regular azure storage.
    1. It can store very large data in peta bytes.
    2. Gives hierarchical data folder structure and granular security at each folder and files level. 
    3. Integrates with Azure AD (Entra) to provide ACL and RBAC.


## Usecase:
For building the use case, we’ll be using an Azure SQL database that is configured with sample data: AdventureWorks. This database will play the role as source from which we’ll be getting the data. The end goal is to build a simple and user-friendly data model that is ready for consumption.

## Architecture
Coming up ...