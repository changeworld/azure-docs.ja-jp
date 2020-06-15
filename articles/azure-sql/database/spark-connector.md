---
title: Microsoft Azure SQL と SQL Server で Spark コネクタを使用する
description: Microsoft Azure SQL と SQL Server 用の Spark コネクタを使用する方法について学習します
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b2e042f2c3a7c6e1528ff96fb4fb96f392274855
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84025683"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Spark コネクタを使用したリアルタイムのビッグ データ分析の高速化 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Spark コネクタを使用すると、Azure SQL Database、Azure SQL Managed Instance、SQL Server データベースを Spark ジョブの入力データ ソースまたは出力データ シンクとして機能させることができます。 ビッグ データ分析の中でリアルタイム トランザクション データを利用でき、アドホック クエリの結果やレポートを保持できます。 組み込みの JDBC コネクタに比べて、このコネクタには、Microsoft Azure SQL データベースと SQL Server データベースにデータを一括挿入する機能があります。 行単位の挿入に比べ、パフォーマンスを 10 倍から 20 倍も向上させることができます。 Spark コネクタは、Azure SQL データベースに接続するための AAD 認証をサポートします。 AAD アカウントを使用して Azure Databricks から Azure SQL データベースに安全に接続できます。 組み込みの JDBC コネクタと同様のインターフェイスを備えています。 この新しいコネクタを使用するための既存の Spark ジョブの移行は簡単に実行できます。

## <a name="download-and-build-spark-connector"></a>Spark コネクタのダウンロードと構築

開始するには、GitHub の [azure-sqldb-spark リポジトリ](https://github.com/Azure/azure-sqldb-spark)から Spark コネクタをダウンロードします。

### <a name="official-supported-versions"></a>正式にサポートされているバージョン

| コンポーネント                             | Version                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 以降           |
| Scala                                 | 2.10 以降            |
| SQL Server 用 Microsoft JDBC ドライバー  | 6.2 以降             |
| Microsoft SQL Server                  | SQL Server 2008 以降 |
| Azure SQL データベース                    | サポートされています                |
| Azure SQL Managed Instance            | サポートされています                |

Spark コネクタでは、SQL Server 用の Microsoft JDBC ドライバーを使用して、Spark ワーカー ノードと SQL データベース間でデータを移動します。

データフローは次のとおりです。

1. Spark のマスター ノードが、Azure SQL データベースまたは SQL Server データベースに接続され、特定のテーブルまたは特定の SQL クエリを使用してデータを読み込みます。
2. Spark のマスター ノードが、変換のためにワーカー ノードにデータを分散します。
3. ワーカー ノードが、Azure SQL データベースまたは SQL Server データベースに接続され、データベースにデータを書き込みます。 ユーザーは、使用する挿入方法 (行単位または一括) を選択できます。

次の図にデータ フローを示します。

   ![アーキテクチャ](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Spark コネクタの構築

現在、コネクタ プロジェクトでは Maven を使用します。 依存関係なしにコネクタを構築するには、次のコードを実行します。

- mvn クリーン パッケージ
- releases フォルダーから最新バージョンの JAR をダウンロードします
- SQL Database Spark JAR をインクルードします

## <a name="connect-and-read-data-using-the-spark-connector"></a>Spark コネクタを使用した接続とデータの読み取り

Spark ジョブから Azure SQL データベースまたは SQL Server データベースに接続して、データの読み取りまたは書き込みを行うことができます。 Azure SQL または SQL Server のデータベースで DML クエリまたは DDL クエリを実行することもできます。

### <a name="read-data-from-azure-sql-and-sql-server"></a>Azure SQL または SQL Server からデータを読み取る

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>指定した SQL クエリによる Azure SQL または SQL Server からのデータの読み込み

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Azure SQL または SQL Server にデータを書き込む

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Azure SQL または SQL Server で DML クエリまたは DDL クエリを実行する

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-to-azure-sql-using-aad-authentication"></a>AAD 認証を使用して Spark から Azure SQL に接続する

Azure Active Directory (AAD) 認証を使用して Azure SQL に接続できます。 AAD 認証は、データベース ユーザーの ID を一元管理するという目的で使用でき、SQL Server 認証に代わる方法となります。

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword 認証モードを使用した接続

#### <a name="setup-requirement"></a>設定要件

ActiveDirectoryPassword 認証モードを使用する場合は、[azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) とその依存関係をダウンロードし、それらを Java ビルド パスに含める必要があります。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>アクセス トークンを使用した接続

#### <a name="setup-requirement"></a>設定要件

アクセス トークンに基づく認証モードを使用する場合は、[azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) とその依存関係をダウンロードし、それらを Java ビルド パスに含める必要があります。

Azure SQL Database または Azure SQL Managed Instance データベースに対するアクセス トークンを取得する方法については、[Azure Active Directory 認証を使用した認証](authentication-aad-overview.md)に関する記事を参照してください。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-and-sql-server-using-bulk-insert"></a>一括挿入を使用して Azure SQL または SQL Server にデータを書き込む

従来の JDBC コネクタは、行単位の挿入を使用して Azure SQL または SQL Server にデータを書き込みます。 Spark コネクタでは、一括挿入を使用して Azure SQL または SQL Server にデータを書き込むことができます。 大きなデータ セットを読み込むとき、または列ストア インデックスが使用されているテーブルにデータを読み込むときの書き込みのパフォーマンスが大幅に向上します。

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>次のステップ

Spark コネクタをまだダウンロードしていない場合は、[GitHub の azure-sqldb-spark リポジトリ](https://github.com/Azure/azure-sqldb-spark)からダウンロードし、リポジトリのその他のリソースを調べます。

- [Azure Databricks ノートブックのサンプル](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [サンプル スクリプト (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

また、[Apache Spark SQL、DataFrames、データセット ガイド](https://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure Databricks ドキュメント](https://docs.microsoft.com/azure/azure-databricks/)を確認することもできます。
