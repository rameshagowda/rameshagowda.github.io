---
title: Kubernetes Ingress routing with TLS and network polices on KinD cluster
date: 2025-03-22 12:00 -500
categories: [Kubernetes, Cilium, CNI, Ingress, TLS]
tags: [cni, ingress, routing, networkpolicies]
author: gowda
---

## Introduction

Outline steps to implement some of the Kubernetes advanced concepts.

1. Create a local Kubernetes in Docker (KinD) cluster with multiple nodes. Kind cluster gives a similar experience as a production Kubernetes cluster on your local desktop.
2. Setup a simple ingress routing using Nginx ingress controller.
3. Introduce TLS encryption to ingress routing.
4. Compare the Network policies between Cilium CNI and Kubernetes CNI.
5. Discuss some of the advantages using Cilium as CNI in networking, observability and service mesh.

## Create Kubernetes KinD cluster locally

- Install KinD from here - <https://kind.sigs.k8s.io/docs/user/quick-start/>
- Create a cluster with 3 nodes - 1 Control node, 2 Worker nodes
- Allow control node to make requests to ingress controller over ports for both http (80) and https (443)

```yaml
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
name: dev
nodes:
  - role: control-plane
    kubeadmConfigPatches:
      - |
        kind: InitConfiguration
        nodeRegistration:
          kubeletExtraArgs:
            node-labels: "ingress-ready=true"
    extraPortMappings:
      - containerPort: 80
        hostPort: 80
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        protocol: TCP
  - role: worker
  - role: worker
```

- Now we have KinD cluster with Ingress enabled.
  ![Desktop View](/assets/img/k8s/kind.png)

## Ingress Routing with Nginx Controller

- Apply Nginx controller

        - kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

- Ingress controller is up and running
  ![Desktop View](/assets/img/k8s/ing.png)

## Application deployment and a service

- Create a deployment.

        - kubectl create deployment my-test-app --image=nginx

- Create a service for the deployment.

        - kubectl expose deployment my-test-app --name=my-test-app-service --type=ClusterIP --port=80 --target-port=80

### Ingress Resource

- Create ingress resource to define routing rules.

        - kubectl create ing my-ingress --rule="myapp.local/my-test-app=my-test-app-service:80" --annotation nginx.ingress.kubernetes.io/rewrite-target=/$2

- This is how Ingress rule looks like after execting the above command.
  ![Desktop View](/assets/img/k8s/ingrule.png)

### Local DNS mapping and Test the APP

        - Update the file /etc/hosts to create a record: 127.0.0.1 myapp.local

        - test it: curl http://example.local -v

- Access it from the browser:
  ![Desktop View](/assets/img/k8s/myapp.png)

## TLS enabled Ingress Resource

### Self Signed certificates

Real production workloads will always be encrypted with Certificate Authority (CA) signed TLS certificates. Here lets create self-signed certificates using using OpenSSL tool. This certificate will enable TLS encryption with ingress routing.

- Cretae a TLS Key.

        - openssl genrsa -out tls.key 2048

- Create TLS certificate - Note the domain name in CN which is matching with my local domain.

        - openssl req -x509 -new -nodes -key ingress-tls.key -subj "/CN=myapp.local” -days 10 -out tls.crt

- Optional - View certificate details

        - openssl x509 -in tls.crt -noout -text

### Kubernetes Secret

A Kubernetes secret for TLS is required. Lets create one.

        - k create secret tls my-secret --cert=tls.crt --key=ingress-tls.key

![Desktop View](/assets/img/k8s/secret.png)

### Enable TLS in Ingress Reource

- Edit the ingress resource to enable TLS or delete and recreate the ingress resource.

        - kubectl create ing my-ingress --rule="myapp.local/my-test-app=my-test-app-service:80,tls=my-secret" --annotation nginx.ingress.kubernetes.io/rewrite-target=/$2

## Architecture

![Desktop View](/assets/img/medallion/medallion-arch.png){: width="800"}

### Create 3 containers in ADLS2 account: bronze, silver and gold

The bronze container will be used for capturing all raw ingested data. We’ll use Parquet files, because no versioning is required. We will use an YYYYMMDD partition scheme for adding newly loaded data to this container.

The silver container will be used for slightly transformed and standardized data. The file format for silver is Delta. For the design, we’ll develop slowly changing dimensions using DBT.

At last, there’s gold, which will be used for the final integrated data. Again, we’ll use DBT to join different datasets together. The file format for gold is Delta as well.

### Create ADF pipeline

Here we weill pull data from AdventureWorks on AzureSQL. Query used

```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE' AND TABLE_SCHEMA = 'SalesLT'
```

Follow this video: <https://www.youtube.com/watch?v=KsO2FHQdILs>. For the entire flow, three datasets are needed: TableList, SQLTable, ParquetTable.

Final state of the ADF configurations are available in this git repo:
<https://github.com/rameshagowda/azuredatafactory>

ADF final state screenshots that shows parameters, linkedservices and calling databricks notebooks from ADF

<!-- ![Desktop View](/assets/img/medallion/ADF-linkedservices.png) -->

<!-- ![Desktop View](/assets/img/medallion/ADF-pipeline.png){: width="800"}
![Desktop View](/assets/img/medallion/linkedservice-blobstorage.png){: width="800"}
![Desktop View](/assets/img/medallion/linkedservice-params-blobstorage.png){: width="800"} -->

Note that ADF calls databricks notebooks that will do data transformation using DBT.

### Configure Databricks and KeyVault

Open Azure databricks workspace
On Azure portal, you either create a serviceprincipal or managed identity so that Databricks can access Keyvault
then copy the ADLS2 account token and store it in KeyVault as Secret.
After creating your secret, ensure that your DataBricks service principle has list and read rights on your secrets. You can configure this under Access Policies.
To validate that everything works as expected, Create a new DataBricks Notebook and type in the following information:

```python
abcd = dbutils.secrets.get('dbtScope','blobAccountKey')
print(abcd)
```

Next, add the mounting points to your storage containers. Create another notebook and execute the following code for adding mounting points to bronze, silver and gold:

```python
#mount bronze
dbutils.fs.mount(
 source='wasbs://bronze@<<storageaccount>>.blob.core.windows.net/',
 mount_point = '/mnt/bronze',
 extra_configs = {'fs.azure.account.key.<<storageaccount>>.blob.core.windows.net': dbutils.secrets.get('dbtScope','blobAccountKey')}
)

#mount silver
dbutils.fs.mount(
 source='wasbs://silver@<<storageaccount>>.blob.core.windows.net/',
 mount_point = '/mnt/silver',
 extra_configs = {'fs.azure.account.key.<<storageaccount>>.blob.core.windows.net': dbutils.secrets.get('dbtScope','blobAccountKey')}
)

#mount gold
dbutils.fs.mount(
 source='wasbs://gold@<<storageaccount>>.blob.core.windows.net/',
 mount_point = '/mnt/gold',
 extra_configs = {'fs.azure.account.key.<<storageaccount>>.blob.core.windows.net': dbutils.secrets.get('dbtScope','blobAccountKey')}
)

dbutils.fs.ls("/mnt/bronze")
dbutils.fs.ls("/mnt/silver")
dbutils.fs.ls("/mnt/gold")

then, create a databricks notebook with dynamic script which accepts parameters from ADF.

#fetch parameters from Azure Data Factory
table_schema=dbutils.widgets.get("table_schema")
table_name=dbutils.widgets.get("table_name")
filePath=dbutils.widgets.get("filePath")

#create database
spark.sql(f'create database if not exists {table_schema}')

#create new external table using latest datetime location
ddl_query = """CREATE OR REPLACE TABLE """+table_schema+"""."""+table_name+"""
                   USING PARQUET
                   LOCATION '/mnt/bronze/"""+filePath+"""/"""+table_schema+"""."""+table_name+""".parquet'
                   """

#execute query
spark.sql(ddl_query)
```

I used below reference to complete my PoC on Azure. Use the link to install and setup DBT. Using DBT we will transform the data and create gold layer.
<https://piethein.medium.com/using-dbt-for-building-a-medallion-lakehouse-architecture-azure-databricks-delta-dbt-31a31fc9bd0>

Final state of databricks and DBT are available in my git repos.
<https://github.com/rameshagowda/azuredatabricks>
<https://github.com/rameshagowda/dbt-azuredatabricks>

## References:

- <https://piethein.medium.com/using-dbt-for-building-a-medallion-lakehouse-architecture-azure-databricks-delta-dbt-31a31fc9bd0>
- <https://www.youtube.com/watch?v=KsO2FHQdILs>
- <https://anujsen02.medium.com/analytics-engineering-on-the-lakehouse-using-dbt-databricks-part-1-c4d773731ffe>
- <https://app.pluralsight.com/library/courses/building-etl-pipeline-microsoft-azure-databricks/table-of-contents>
- <https://www.youtube.com/watch?v=x3-qUw9XWMA>
