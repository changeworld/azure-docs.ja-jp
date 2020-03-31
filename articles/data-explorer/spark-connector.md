---
title: Apache Spark 向け Azure Data Explorer コネクタを使用し、Azure Data Explorer と Spark クラスターの間でデータを移動します。
description: このトピックでは、Azure Data Explorer と Spark クラスターの間でデータを移動する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208605"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Apache Spark 用の Azure Data Explorer コネクタ

[Apache Spark](https://spark.apache.org/) は、"大規模なデータ処理のための統合された分析エンジン" です。 Azure Data Explorer は、大量のデータのリアルタイム分析を実現するフル マネージドの高速データ分析サービスです。 

Spark 用の Azure Data Explorer コネクタは、あらゆる Spark クラスターで実行できる[オープン ソース プロジェクト](https://github.com/Azure/azure-kusto-spark)です。 Azure Data Explorer と Spark クラスター間でデータを移動するためのデータ ソースとデータ シンクを実装します。 Azure Data Explorer と Apache Spark を使用して、データ ドリブン シナリオをターゲットとする、高速でスケーラブルなアプリケーションを作成することができます。 たとえば、機械学習 (ML)、ETL (抽出 - 読み込み - 変換)、および Log Analytics などです。 このコネクタにより、Azure Data Explorer は、書き込み、読み取り、writeStream などの標準的な Spark のソースおよびシンク操作に有効なデータ ストアになります。

Azure Data Explorer には、バッチ モードまたはストリーミング モードのいずれでも書き込むことができます。 Azure Data Explorer からの読み取りでは、列の排除と述語のプッシュダウンがサポートされています。これにより、Azure Data Explorer でデータがフィルター処理されるため、転送されるデータの量が減ります。

このトピックでは、Azure Data Explorer Spark コネクタをインストールして構成し、Azure Data Explorer と Apache Spark クラスター間でデータを移動する方法について説明します。

> [!NOTE]
> 下の例のいくつかでは [Azure Databricks](https://docs.azuredatabricks.net/) Spark クラスターが登場しますが、Azure Data Explorer Spark コネクタは、Databricks やその他の Spark ディストリビューションに直接依存しません。

## <a name="prerequisites"></a>前提条件

* [Azure Data Explorer クラスターとデータベースを作成します](/azure/data-explorer/create-cluster-database-portal) 
* Spark クラスターの作成
* Azure Data Explorer コネクタ ライブラリのインストール:
    * [Spark 2.4、Scala 2.11 向けに事前構築されたライブラリ](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) がインストールされていること

> [!TIP]
> 2.3.x バージョンもサポートされていますが、pom.xml の依存関係を場合によっては変更する必要があります。

## <a name="how-to-build-the-spark-connector"></a>Spark コネクタのビルド方法

> [!NOTE]
> この手順は省略可能です。 事前構築されたライブラリを使用する場合、[Spark クラスターの設定](#spark-cluster-setup)にお進みください。

### <a name="build-prerequisites"></a>構築の前提条件

1. 次の [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) ライブラリを含む、[依存関係](https://github.com/Azure/azure-kusto-spark#dependencies)に記載されているライブラリをインストールします。
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Spark コネクタの構築については、[こちらのソース](https://github.com/Azure/azure-kusto-spark)を参照してください。

1. Maven プロジェクト定義を使用する Scala/Java アプリケーションの場合は、アプリケーションを次の成果物にリンクしてください (最新版は異なる場合があります)。
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>ビルド コマンド

Jar をビルドして、すべてのテストの実行するには:

```
mvn clean package
```

Jar をビルドし、すべてのテストを実行し、ローカルの Maven リポジトリに jar をインストールするには:

```
mvn clean install
```

詳細については、[コネクタの使用](https://github.com/Azure/azure-kusto-spark#usage)に関するページを参照してください。

## <a name="spark-cluster-setup"></a>Spark クラスターの設定

> [!NOTE]
> 次の手順を実行するときは、最新の Azure Data Explorer Spark コネクタ リリースを使用することをお勧めします。

1. Spark 2.4.4 と Scala 2.11 を使用し、Azure Databricks クラスターに基づいて Spark クラスターを次のように構成します。

    ![Databricks クラスターの設定](media/spark-connector/databricks-cluster.png)
    
1. Maven から最新の spark-kusto-connector ライブラリをインストールします。
    
    ![ライブラリのインポート](media/spark-connector/db-libraries-view.png) ![Spark-Kusto-Connector の選択](media/spark-connector/db-dependencies.png)

1. 必要なライブラリがすべてインストールされていることを確認します。

    ![ライブラリがインストールされていることを確認します](media/spark-connector/db-libraries-view.png)

1. JAR ファイルを使用したインストールの場合は、追加の依存関係がインストールされていることを確認します。

    ![依存関係を追加する](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>認証

Azure Data Explorer Spark コネクタを使用すると、次のいずれかの方法を使用して Azure Active Directory (Azure AD) で認証することができます。
* [Azure AD アプリケーション](#azure-ad-application-authentication)
* [Azure AD アクセス トークン](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [デバイス認証](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (非運用環境シナリオの場合)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)。Key Vault リソースにアクセスするには、azure-keyvault パッケージをインストールし、アプリケーションの資格情報を提供します。

### <a name="azure-ad-application-authentication"></a>Azure AD アプリケーション認証

Azure AD アプリケーション認証は、最も単純で最も一般的な認証方法であり、Azure Data Explorer Spark コネクタにお勧めします。

|Properties  |説明  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD アプリケーション (クライアント) ID。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 認証機関。 Azure AD Directory (テナント) ID。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    クライアントの Azure AD アプリケーション キー。     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer の特権

Azure Data Explorer クラスターに対して次の特権を付与します。

* 読み取り (データ ソース) の場合、Azure AD ID にターゲット データベースに対する "*ビューアー*" 特権、ターゲット テーブルの "*管理者*" 特権が必要です。
* 書き込み (データ シンク) の場合、Azure AD ID にターゲット データベースに対する "*インジェスター*" 特権が必要です。 新しいテーブルを作成するには、対象データベースの*ユーザー*特権も与える必要があります。 ターゲット テーブルが既に存在する場合、ターゲット テーブルに対する "*管理者*" 特権を構成する必要があります。
 
Azure Data Explorer のプリンシパル ロールの詳細については、[ロールベースの承認](/azure/kusto/management/access-control/role-based-authorization)に関するページを参照してください。 セキュリティ ロールの管理については、「[security roles management](/azure/kusto/management/security-roles)」(セキュリティ ロールの管理) を参照してください。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark シンク: Azure Data Explorer に書き込む

1. シンクのパラメーターの設定:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark DataFrame を Azure Data Explorer クラスターにバッチとして書き込む:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   または、簡略化された構文を使用します。
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. ストリーミング データを書き込む:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark ソース: Azure Data Explorer から読み取る

1. [少量のデータ](/azure/kusto/concepts/querylimits)を読み込む場合は、データ クエリを定義します。

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 省略可能:**自分で**一時的な BLOB ストレージ (Azure Data Explorer ではなく) を提供する場合は、作成される BLOB は呼び出し元の責任になります。 これには、ストレージのプロビジョニング、アクセス キーのローテーション、および一時的な成果物の削除が含まれます。 
    KustoBlobStorageUtils モジュールには、アカウントとコンテナーの座標およびアカウントの資格情報、または書き込み、読み取り、リストの権限を持つ完全な SAS URL のいずれかに基づいて、BLOB を削除するためのヘルパー関数が含まれています。 対応する RDD が不要になると、トランザクションごとに一時的な BLOB 成果物が別のディレクトリに格納されます。 このディレクトリは、Spark ドライバー ノードで報告される読み取りトランザクション情報ログの一部としてキャプチャされます。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    上記の例では、コネクタ インターフェイスを使用して Key Vault にアクセスすることはできません。より簡単な Databricks シークレットを使用する方法が使用されます。

1. Azure Data Explorer から読み取ります。

    * **自分で**一時的な BLOB ストレージを提供する場合は、次のように Azure Data Explorer から読み取ります。

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * **Azure Data Explorer** によって一時的な BLOB ストレージが提供される場合は、次のように Azure Data Explorer から読み取ります。
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer Spark コネクタ](https://github.com/Azure/azure-kusto-spark/tree/master/docs)についての詳細情報
* [Java および Python のサンプル コード](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
