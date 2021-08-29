---
title: Azure SQL と SQL Server
description: この記事では、コネクタを使用して、Azure MS SQL プールとサーバーレス Apache Spark プールの間でデータを移動する方法について説明します。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 05/19/2020
ms.custom: has-adal-ref
ms.openlocfilehash: da64e27b5ae4675b313007c9b185db9194607f2c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428831"
---
# <a name="azure-sql-database-and-sql-server-connector-for-apache-spark"></a>Apache Spark の Azure SQL Database および SQL Server 用コネクタ
Azure SQL Database と SQL Server 用の Apache Spark コネクタを使用すると、これらのデータベースを Apache Spark ジョブの入力データ ソースおよび出力データ シンクとして機能させることができます。 ビッグ データ分析の中でリアルタイム トランザクション データを利用でき、アドホック クエリの結果やレポートを保持できます。

組み込みの JDBC コネクタに比べて、このコネクタには、SQL データベースにデータを一括挿入する機能があります。 行単位の挿入に比べ、パフォーマンスを 10 倍から 20 倍も向上させることができます。 SQL Server および Azure SQL Database 用の Spark コネクタは、Azure Active Directory (Azure AD) [認証](/sql/connect/spark/connector#azure-active-directory-authentication)もサポートしています。この機能を使用すると、Azure Synapse Analytics から Azure SQL データベースに安全に接続できます。 

この記事では、DataFrame API を使用して、MS SQL コネクタで SQL データベースに接続する方法について説明します。 この記事では、PySpark API を使用した詳細な例を示します。 MS SQL コネクタを使用して SQL データベースに接続する場合にサポートされているすべての引数とサンプルについては、[Azure Data SQL のサンプル](https://github.com/microsoft/sql-server-samples#azure-data-sql-samples-repository)に関するページをご覧ください。


  
## <a name="connection-details"></a>接続の詳細情報
この例では、Microsoft Spark ユーティリティを使用して、構成済みのキー コンテナーからシークレットを取得できるようにします。 Microsoft Spark ユーティリティの詳細については、[Microsoft Spark ユーティリティの概要](../microsoft-spark-utilities.md)に関するページを参照してください。

```python
servername = "<< server name >>"
dbname = "<< database name >>"
url = servername + ";" + "databaseName=" + dbname + ";"
dbtable = "<< table name >> "
user = "<< username >>" 
password = mssparkutils.credentials.getSecret('azure key vault name','secret name')
```

> [!NOTE]
> 現在、Azure SQL コネクタでは、リンク サービスまたは AAD パススルーはサポートされていません。

## <a name="use-the-azure-sql-and-sql-server-connector"></a>Azure SQL および SQL Server コネクタを使用する

### <a name="read-data"></a>データの読み取り
```python
#Read from SQL table using MS SQL Connector
print("read data from SQL server table  ")
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("user", user) \
        .option("password", password).load()

jdbcDF.show(5)
```

### <a name="write-data"></a>データを書き込む
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", dbtable) \
    .option("user", user) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("MSSQL Connector write failed", error)

print("MSSQL Connector write(overwrite) succeeded  ")
```
### <a name="append-data"></a>データを追加する
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

### <a name="python-example-with-service-principal"></a>サービス プリンシパルを使用した Python の例
```python
import adal

# Located in App Registrations from Azure Portal
tenant_id = "<< tenant id >> "

# Located in App Registrations from Azure Portal
resource_app_id_url = "https://database.windows.net/"

# Authority
authority = "https://login.windows.net/" + tenant_id

context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>Active Directory パスワードを使用した Python の例
```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

## <a name="next-steps"></a>次のステップ
- [SQL Server と Azure SQL のコネクタの詳細情報](/sql/connect/spark/connector)
- [Azure Data SQL のサンプルを確認する](https://github.com/microsoft/sql-server-samples)
