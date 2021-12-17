---
title: Azure HDInsight 内の Hive Warehouse Connector API
description: Hive Warehouse Connector のさまざまな API について説明します。
author: adesh-rao
ms.author: adrao
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/29/2021
ms.openlocfilehash: 4816830feac9aceb34ecfaa701ea0b7aec9c728b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780866"
---
# <a name="hive-warehouse-connector-apis-in-azure-hdinsight"></a>Azure HDInsight 内の Hive Warehouse Connector API

この記事では、Hive Warehouse Connector でサポートされているすべての API の一覧を示します。 以下に示すすべての例は、spark-shell と Hive Warehouse Connector セッションを使用して実行されます。

Hive Warehouse Connector セッションを作成する方法:

```scala
import com.hortonworks.hwc.HiveWarehouseSession
val hive = HiveWarehouseSession.session(spark).build()
```

## <a name="prerequisite"></a>前提条件

[Hive Warehouse Connector の設定](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)ステップを完了します。


## <a name="supported-apis"></a>サポート対象 API

- データベースを設定する:
    ```scala
    hive.setDatabase("<database-name>")
    ```

- すべてのデータベースを一覧表示する:
    ```scala
    hive.showDatabases()
    ```

- 現在のデータベース内のすべてのテーブルを一覧表示する
    ```scala
    hive.showTables()
    ```

- テーブルについて説明する
    
    ```scala
   // Describes the table <table-name> in the current database
    hive.describeTable("<table-name>")
    ```
    
    ```scala
   // Describes the table <table-name> in <database-name>
    hive.describeTable("<database-name>.<table-name>")
    ```

- データベースの削除
    
    ```scala
   // ifExists and cascade are boolean variables
    hive.dropDatabase("<database-name>", ifExists, cascade)
    ```

- 現在のデータベース内のテーブルを削除する
    
    ```scala
    // ifExists and purge are boolean variables
    hive.dropTable("<table-name>", ifExists, purge)
    ```

- データベースを作成する
    ```scala
   // ifNotExists is boolean variable
    hive.createDatabase("<database-name>", ifNotExists)
    ```

- 現在のデータベース内にテーブルを作成する
    ```scala
   // Returns a builder to create table
    val createTableBuilder = hive.createTable("<table-name>")
    ```
    
    create-table のビルダーでは、次の操作だけがサポートされます。 
    
    ```scala
   // Create only if table does not exists already
    createTableBuilder = createTableBuilder.ifNotExists()
    ```
    
    ```scala
   // Add columns
    createTableBuilder = createTableBuilder.column("<column-name>", "<datatype>")
    ```
    
    ```scala
    // Add partition column
    createTableBuilder = createTableBuilder.partition("<partition-column-name>", "<datatype>")
    ```
    ```scala
   // Add table properties
    createTableBuilder = createTableBuilder.prop("<key>", "<value>")
    ```
    ```scala
    // Creates a bucketed table,
    // Parameters are numOfBuckets (integer) followed by column names for bucketing
    createTableBuilder = createTableBuilder.clusterBy(numOfBuckets, "<column1>", .... , "<columnN>")
    ```
    
    ```scala
    // Creates the table
    createTableBuilder.create()
    ```

    > [!NOTE]
    > この API により、既定の場所に ORC 形式のテーブルが作成されます。 その他の機能やオプションの場合、または Hive クエリを使用してテーブルを作成するには、`executeUpdate` API を使用します。


- テーブルの読み取り

    ```scala
   // Returns a Dataset<Row> that contains data of <table-name> in the current database
    hive.table("<table-name>")
    ```

- HiveServer2 に対して DDL コマンドを実行する 

    ```scala
    // Executes the <hive-query> against HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>")
    ```
    
    ```scala
    // Executes the <hive-query> against HiveServer2
    // Throws exception, if propagateException is true and query threw excpetion in HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>", propagateException) // propagate exception is boolean value
    ```

- Hive クエリを実行し、データセットに結果を読み込む
    
  - LLAP デーモンを介してクエリを実行する。 **[推奨]**
    ```scala
    // <hive-query> should be a hive query 
    hive.executeQuery("<hive-query>")
    ```
  - JDBC を介して HiveServer2 を通じてクエリを実行する。

    Spark セッションを開始してこの API を使用する前に、Spark 構成で `spark.datasource.hive.warehouse.smartExecution` を `false` に設定します
    ```scala
    hive.execute("<hive-query>")
    ```

- Hive Warehouse Connector セッションを終了する
    ```scala
    // Closes all the open connections and
    // release resources/locks from HiveServer2
    hive.close()
    ```

- Hive マージ クエリを実行する
    
    この API では、次の形式の Hive マージ クエリが作成されます
    
    ```
    MERGE INTO <current-db>.<target-table> AS <targetAlias> USING <source expression/table> AS <sourceAlias>
    ON <onExpr>
    WHEN MATCHED [AND <updateExpr>] THEN UPDATE SET <nameValuePair1> ... <nameValuePairN>
    WHEN MATCHED [AND <deleteExpr>] THEN DELETE
    WHEN NOT MATCHED [AND <insertExpr>] THEN INSERT VALUES <value1> ... <valueN>
    ```

    ```scala
    val mergeBuilder = hive.mergeBuilder() // Returns a builder for merge query
    ```
    ビルダーでは次の操作がサポートされます。
    
    ```scala
    mergeBuilder.mergeInto("<taget-table>", "<targetAlias>")
    ```
    
    ```scala
    mergeBuilder.using("<source-expression/table>", "<sourceAlias>")
    ```
    
    ```scala
    mergeBuilder.on("<onExpr>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenUpdate("<updateExpr>", "<nameValuePair1>", ... , "<nameValuePairN>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenDelete("<deleteExpr>")
    ```
    
    ```scala
    mergeBuilder.whenNotMatchedInsert("<insertExpr>", "<value1>", ... , "<valueN>");
    ```

    ```scala
    // Executes the merge query
    mergeBuilder.merge()
    ```

- データセットを Hive テーブルにバッチで書き込む
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector")
       .option("table", tableName)
       .mode(SaveMode.Type)
       .save()
    ```
   - TableName は `<db>.<table>` または `<table>` の形式である必要があります。 データベース名が指定されていない場合、テーブルは現在のデータベース内で検索または作成されます
    
   - SaveMode の種類は次のとおりです。
    
     - 追加: 指定されたテーブルにデータセットを追加します
    
     - 上書き: 指定されたテーブル内のデータをデータセットで上書きします
    
     - 無視: テーブルが既に存在する場合は書き込みをスキップし、エラーはスローしません
    
     - ErrorIfExists: テーブルが既に存在する場合にエラーをスローします


- HiveStreaming を使用して Hive テーブルにデータセットを書き込む
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("metastoreUri", "<HMS_URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    
     // To write to static partition
     df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("partition", partition)
       .option("metastoreUri", "<HMS URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    ```
    > [!NOTE]
    > ストリーム書き込みでは常にデータが追加されます。


- Hive テーブルへの Spark ストリームの書き込み
    ```scala
    stream.writeStream
        .format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource")
        .option("metastoreUri", "<HMS_URI>")
        .option("database", databaseName)
        .option("table", tableName)
      //.option("partition", partition) , add if inserting data in partition
      //.option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
        .start()
    ```
