---
title: Azure Synapse Spark プールに外部 Hive メタストアを使用する
description: Azure Synapse Spark プールに外部 Hive メタストアを設定する方法を説明します。
keywords: 外部 Hive メタストア、共有、Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 805987eb38df3979904fc8b9f3bebfc7fdb1fdca
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276380"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>Azure Synapse Spark プールに外部 Hive メタストアを使用する (プレビュー)

Azure Synapse Analytics では、同じワークスペース上の Apache Spark プール間で、マネージド HMS (Hive Metastore Service) 互換メタストアをカタログとして共有できます。 お客様が Hive カタログをワークスペースの外部に保持し、カタログ オブジェクトをワークスペース外の他の計算エンジン (HDInsight や Azure Databricks など) と共有したい場合は、外部 Hive メタストアに接続できます。 この記事では、Synapse Spark を外部の Apache Hive Metastore に接続する方法を説明します。 

## <a name="supported-hive-metastore-versions"></a>サポートしている Hive メタストアのバージョン

この機能は、Spark 2.4 と Spark 3.0 のどちらでも利用できます。 次のテーブルに、Spark の各バージョンでサポートしている Hive メタストア サービス (HMS) のバージョンを記載します。


|Spark バージョン|HMS 1.2.X|HMS 2.1.X|HMS 2.3.x|HMS 3.1.X|
|--|--|--|--|--|
|2.4|はい|はい|はい|いいえ|
|3|はい|はい|はい|はい|

## <a name="set-up-hive-metastore-linked-service"></a>Hive メタストアのリンク サービスをセットアップする

> [!NOTE]
> 外部 Hive メタストアには、Azure SQL Database だけをサポートしています。

次の手順で、外部 Hive メタストアのリンク サービスを Synapse ワークスペースにセットアップします。

1. Synapse Studio を開き、 **[Manage]\(管理\) > [Linked services]\(リンク サービス\)** に移動し、 **[New]\(新規\)** をクリックして新しいリンク サービスを作成します。

   :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="Hive Metastore のリンク サービスをセットアップする" border="true":::

2. **[Azure SQL Database]** を選択して **[Continue]\(続行\)** をクリックします。

3. リンク サービスの **[Name]\(名前\)** を入力します。 リンク サービスの名前を控えます。この情報はすぐ後で Spark の構成に使用します。

4. 外部 Hive メタストアの Azure SQL Database を Azure サブスクリプションのリストから選ぶか、手動でその情報を入力します。

5. 現在サポートしている認証の種類は SQL 認証だけです。 **[User name]\(ユーザー名\)** と **[Password]\(パスワード\)** を入力して接続をセットアップします。

6. 接続をテスト (**Test connection**) して、ユーザー名とパスワードが有効であることを確認します。

7. **[Create]\(作成\)** をクリックしてリンク サービスを作成します。 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>接続をテストし、ノートブックでメタストアのバージョンを取得する
ネットワークによっては、Spark プールから外部 Hive メタストア DB へのアクセスを遮断するよう、セキュリティ規則を設定している場合があります。 Spark プールを設定する前に、任意の Spark プール ノートブックで下のコードを実行して、外部 Hive メタストア DB への接続をテストします。 

その出力結果から Hive メタストアのバージョンも取得できます。 Hive メタストアのバージョンは Spark の構成で使用します。

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>外部 Hive メタストアを使用するよう Spark を設定する
外部 Hive メタストアのリンク サービスを作成したら、その外部 Hive メタストアを使用するために、Spark でいくつかの構成を設定する必要があります。 Spark プール単位でも、Spark セッション単位でも、構成を設定できます。 

以下は、構成とその説明です。

> [!NOTE]
> 既定の Hive メタストア バージョンは 2.3 です。 Hive メタストア バージョンが 2.3 の場合、`spark.sql.hive.metastore.version` と `spark.sql.hive.metastore.jars` を設定する必要はありません。 `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name` のみが必要です。

|Spark の構成|説明|
|--|--|
|`spark.sql.hive.metastore.version`|サポートされているバージョン: <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> 最初の 2 つの部分だけを使用し、3 つ目の部分は使用しないようにします|
|`spark.sql.hive.metastore.jars`|<ul><li>Version 1.2: `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 2.1: `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 3.1: `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Azure SQL Database に対して作成したリンク サービスの名前。|

### <a name="configure-spark-pool"></a>Spark プールを設定する 
Spark プールを作成するときは、 **[Additional Settings]\(追加設定\)** タブの **[Apache Spark configuration]\(Apache Spark の構成\)** セクションで、下の構成を書き込んだテキスト ファイルをアップロードします。 既存の Spark プールのコンテキスト メニューでも、Apache Spark の構成を選択してこれらの構成に追加できます。

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="Spark プールを設定する":::

メタストアのバージョンとリンク サービスの名前を更新し、Spark プールの構成に使用するテキスト ファイルに下の構成を書き込んで保存します。

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

メタストア バージョン 2.1 と、HiveCatalog21 という名前のリンク サービスを使用する例を挙げます。

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Spark セッションを設定する
Spark プールを構成したくない場合は、%%configure magic コマンドを使用して、ノートブックで Spark セッションを構成することもできます。 次にコードを示します。 Spark バッチ ジョブにも同じ構成を使用できます。 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>クエリを実行して接続を確認する
これらすべての設定を終えたら、Spark ノートブックで下のクエリを実行してカタログ オブジェクトのリストを取得し、外部 Hive Metastore への接続を確認してみてください。
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>ストレージの接続をセットアップする
Hive メタストア データベースのリンク サービスでは、Hive カタログ メタデータへのアクセスだけを提供します。 既存のテーブルに対するクエリを実行するには、Hive テーブルのデータ本体を保存しているストレージ アカウントへの接続をセットアップする必要があります。

### <a name="set-up-connection-to-adls-gen-2"></a>ADLS Gen 2 への接続をセットアップする
#### <a name="workspace-primary-storage-account"></a>ワークスペース プライマリ ストレージ アカウント
Hive テーブルの基になるデータがワークスペース プライマリ ストレージ アカウントに格納されている場合、追加の設定を行う必要はありません。 ワークスペース作成時に、説明どおりストレージをセットアップすれば、問題なく機能します。

#### <a name="other-adls-gen-2-account"></a>その他の ADLS Gen 2 アカウント
Hive カタログのデータ本体を他の ADLS Gen 2 アカウントに保存している場合は、Spark クエリを実行するユーザーが、ADLS Gen2 ストレージ アカウントで **Storage Blob Data Contributer** ロールを持っている必要があります。 

### <a name="set-up-connection-to-blob-storage"></a>Blob Storage への接続をセットアップする
Hive テーブルのデータの本体を Azure Blob ストレージ アカウントに保存している場合は、下の手順で接続をセットアップします。

1. Synapse Studio を開き、 **[Data]\(データ\) > [Linked]\(リンク\) タブ > [Add]\(追加\)** > **[Connect to external data]\(外部データへの接続\)** に移動します。

   :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="ストレージ アカウントに接続する" border="true":::

2. **[Azure Blob Storage]** を選択して **[Continue]\(続行\)** をクリックします。
3. リンク サービスの **[Name]\(名前\)** を入力します。 リンク サービスの名前を控えます。この情報はすぐ後で Spark セッションの構成に使用します。
4. Azure Blob Storage アカウントを選択します。 認証方法を **[Account key]\(アカウント キー\)** に設定します。 現在 Spark プールでは、アカウント キーでのみ Blob Storage アカウントにアクセスできます。
5. 接続をテスト (**Test connection**) して、 **[Create]\(作成\)** をクリックします。
6. Blob Storage アカウントのリンク サービスを作成した後、Spark クエリを実行するときは、ノートブックで下の Spark コードを実行し、Spark セッションを使用する Blob Storage アカウントへのアクセス権を取得してください。 この手順が必要な理由を[ここ](./apache-spark-secure-credentials-with-tokenlibrary.md)で説明しています。

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

ストレージの接続をセットアップしたら、Hive Metastore の既存のテーブルに対するクエリを実行できます。

## <a name="known-limitations"></a>既知の制限事項

- Synapse Studio オブジェクト エクスプローラーでは当面、外部 HMS の代わりにマネージド Synapse メタストアのオブジェクトを表示します。この機能は現在改善中です。
- [SQL <-> spark 同期](../sql/develop-storage-files-spark-tables.md)は、外部 HMS の使用中は機能しません。  
- 外部 Hive Metastore データベースには、Azure SQL Database だけをサポートしています。 認証には、SQL 認証だけをサポートしています。
- 現在 Spark では、外部 Hive テーブルと、トランザクション以外の ACID マネージド以外の Hive テーブルのみを使用できます。 Hive の ACID テーブルとトランザクション テーブルは、現在サポートされていません。
- Apache Ranger との連携機能は現時点で実装していません。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>Blob Storage に保存したデータを取得するクエリを Hive テーブルに対して実行すると、下のエラーが発生する
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

リンク サービスを通じてストレージ アカウントに対するキー認証を行うときは、Spark セッションのトークンを取得する追加手順が必要です。 下のコードを実行して Spark セッションを設定してから、クエリを実行します。 この手順が必要な理由をここで説明しています。

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>ADLS Gen2 アカウントに保存したテーブルに対してクエリを実行すると、下のエラーが発生する
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

これは、Spark クエリを実行するユーザーが、基になるストレージ アカウントへの十分なアクセス権を持っていないために発生する可能性があります。 Spark クエリを実行するユーザーが、ADLS Gen2 ストレージ アカウントで **Storage Blob Data Contributor** ロールを持っていることを確認してください。 このステップは、リンク サービスの作成後に実行できます。

### <a name="hms-schema-related-settings"></a>HMS スキーマに関する設定 
HMS のバックエンド スキーマおよびバージョンが変更されることを避けるため、次の hive 構成がシステムによって既定で設定されます。 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

Hive には、spark.hadoop.hive.metastore.schema.verification を true に設定すると、HMS のバージョンが 1.2.1 または 1.2.2 である場合も、バージョンが 1.2.0 であることを求めるという問題があります。 回避策として、HMS のバージョンを 1.2.0 に変更するか、次の 2 つの構成を上書きできます。

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

HMS バージョンを変更する必要がある場合は、[hive スキーマ ツール](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)の使用をお勧めします。 また、HMS を HDInsight クラスターで使用している場合は、[HDI に対応しているバージョン](../../hdinsight/interactive-query/apache-hive-migrate-workloads.md)の使用をお勧めします。 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>メタストアを HDInsight 4.0 Spark クラスターで共有したときに、テーブルが表示されない
Hive カタログを HDInsiht 4.0 の spark クラスターで共有する場合は、Synapse spark の `spark.hadoop.metastore.catalog.default` プロパティが HDInsight spark の値に正しく対応していることを確認してください。 既定値は `Spark` です。

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>Hive メタストアを HDInsight 4.0 Hive クラスターで共有したときに、テーブルのリストは表示されるが、テーブルに対するクエリを実行すると空の結果しか取得できない
制限事項で説明したように、Synapse Spark プールでは、外部 Hive テーブルと、トランザクション以外の ACID マネージド以外のテーブルのみがサポートされ、Hive の ACID テーブルとトランザクション テーブルは現在サポートされていません。 HDInsight 4.0 Hive クラスターでは、既定では、すべてのマネージド テーブルが ACID テーブルまたはトランザクション テーブルとして作成されます。それらのテーブルに対してクエリを実行すると空の結果が得られるのはそのためです。 