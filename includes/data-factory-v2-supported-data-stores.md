---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 03/19/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4a47b30b30e15bbd3df4f70b2a6f63b4ab167aea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
| カテゴリ | データ ストア | ソースとしてサポート | シンクとしてサポート | [Azure IR](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) がサポート | [セルフホステッド IR](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) がサポート |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob Storage](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database for MySQL](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure Database for PostgreSQL](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure File Storage](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Database](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Search インデックス](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure Table Storage](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **データベース** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill (ベータ)](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum (ベータ)](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala (ベータ)](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  |
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  |
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | | |✓  |
| &nbsp; |[Netezza (ベータ)](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | | |✓  |
| &nbsp; |[Presto (ベータ)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | | |✓  |
| &nbsp; |[Vertica (ベータ)](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  |
| **NoSQL** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase (ベータ)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  |
| **ファイル** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[ファイル システム](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓ |✓  |
| **汎用プロトコル** |[汎用 HTTP](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[汎用 OData](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[汎用 ODBC](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  |
| **サービスとアプリ** |[Amazon Marketplace Web Service (ベータ)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Common Data Service for Apps](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Concur (ベータ)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[GE Historian](../articles/data-factory/connector-odbc.md#ge-historian-source) |✓ | | |✓  |
| &nbsp; |[HubSpot (ベータ)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira (ベータ)](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento (ベータ)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo (ベータ)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua (ベータ)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Paypal (ベータ)](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks (ベータ)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce Service Cloud](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  |
| &nbsp; |[Shopify (ベータ)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square (ベータ)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[Web テーブル (HTML テーブル)](../articles/data-factory/connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero (ベータ)](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho (ベータ)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> "*ベータ*" と記載されたコネクタは試用版です。フィードバックをお寄せください。 ソリューションでベータ版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。