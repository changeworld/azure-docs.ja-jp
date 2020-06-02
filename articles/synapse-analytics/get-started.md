---
title: チュートリアル:Azure Synapse Analytics の使用を開始する
description: Azure Synapse の基本的な概念をすばやく理解するための手順
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: dcad90713227e55437523c91997175242078e9e4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836483"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics の使用を開始する

このチュートリアルでは、Azure Synapse Analytics を設定して使用するために必要な、すべての基本的な手順について説明します。

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Synapse ワークスペースで使用するストレージ アカウントを準備する

1. [Azure portal](https://portal.azure.com) を開きます。
1. 次の手順で新しいストレージ アカウントを作成します。
    * **[基本]** タブ

    |設定 | 推奨値 | 説明 |
    |---|---|---|
    |**Storage account name \(ストレージ アカウント名\)**| 任意の名前を付けることができます。|このドキュメントでは、`contosolake` として説明します。
    |**アカウントの種類**|`StorageV2` に設定されている必要があります。||
    |**場所**|任意の場所を選択できます。| Synapse ワークスペースと Azure Data Lake Storage (ADLS) Gen2 アカウントが同じリージョン内にあるようにすることをお勧めします。|
    ||||
    
    * **[詳細設定]** タブ
    
    |設定 | 推奨値 | 説明 |
    |---|---|---|
    |**Data Lake Storage Gen2**|`Enabled`| Azure Synapse は、この設定が有効になっているストレージ アカウントでのみ機能します。|
    ||||

1. ストレージ アカウントが作成されたら、左側のナビゲーションから **[アクセス制御 (IAM)]** を選択します。 次に、以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。 
    a. * ストレージ アカウントの**所有者**ロールに自分自身を割り当てます。b. * ストレージ アカウントの**ストレージ BLOB データ所有者**ロールに自分自身を割り当てます。
1. 左側のナビゲーションで **[コンテナー]** を選択し、コンテナーを作成します。 任意の名前を付けることができます。 既定の**パブリック アクセス レベル**をそのまま使用します。 このドキュメントでは、コンテナーを `users` と呼びます。 **［作成］** を選択します 

## <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で `Synapse` を検索します。
1. **[サービス]** の検索結果で、 **[Azure Synapse Analytics (ワークスペース プレビュー)]** を選択します。
1. **[+ 追加]** を選択します。
1. **[基本]** タブ:

    |設定 | 推奨値 | 説明 |
    |---|---|---|
    |**ワークスペース名**|任意の名前にすることができます。| このドキュメントでは、`myworkspace` を使用します。
    |**リージョン**|ストレージ アカウントのリージョンに一致させます。||
    |||

1. **[Data Lake Storage Gen 2]** で、先ほど作成したアカウントとコンテナーを選択します。
    > [!NOTE]
    > ここで選択したストレージ アカウントを Synapse ワークスペースの "プライマリ" ストレージ アカウントと呼びます。 このアカウントは、Apache Spark テーブルにデータを格納するために、また、Spark プールの作成時または Spark アプリケーションの実行時に作成されるログのために使用されます。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **［作成］** を選択します ワークスペースの準備は数分で完了します。

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Synapse ワークスペースの MSI にストレージ アカウントへのアクセス権が与えられていることを確認する

これは既に行われている可能性があります。 いずれの場合も、ご自身で確認する必要があります。

1. [Azure portal](https://portal.azure.com) を開き、ワークスペース用に選択したプライマリ ストレージ アカウントを開きます。
1. 左側のナビゲーションから **[アクセス制御 (IAM)]** を選択します。 次に、以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。 
    a. ストレージ アカウントでワークスペース ID を**ストレージ BLOB データ共同作成者**ロールに割り当てます。 ワークスペース ID には、ワークスペースと同じ名前が付けられます。 このドキュメントでは、ワークスペース名は `myworkspace` であるため、ワークスペース ID は `myworkspaced` です。
1. **[保存]** を選択します。
    
## <a name="launch-synapse-studio"></a>Synapse Studio を起動する

Synapse ワークスペースが作成された後、Synapse Studio を開くには、次の 2 つの方法があります。
* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開き、 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* https://web.azuresynapse.net に直接アクセスし、ワークスペースにサインインします。

## <a name="create-a-sql-pool"></a>SQL プールを作成する

1. Synapse Studio の左側のナビゲーションで、 **[管理] > [SQL プール]** を選択します。

    > [!NOTE] 
    > すべての Synapse ワークスペースには、**SQL オンデマンド**と呼ばれる事前に作成されたプールが付属しています。

1. **[+ 新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**SQL プール名**| `SQLDB1`|
    |**パフォーマンス レベル**|`DW100C`|
    |||

1. **[確認および作成]** を選択し、次に **[作成]** を選択します。
1. SQL プールの準備は数分で完了します。

    > [!NOTE]
    > Synapse SQL プールは、"Azure SQL Data Warehouse" と呼ばれていたものに相当します。

SQL プールが実行されている限り、課金対象のリソースが消費されます。 そのため、必要に応じてプールを一時停止してコストを削減できます。

SQL プールを作成すると、その SQL プールは、**SQLDB1** とも呼ばれる SQL プール データベースに関連付けられます。

## <a name="create-an-apache-spark-pool"></a>Apache Spark プールを作成する

1. Synapse Studio で、左側の **[管理] > [Apache Spark プール]** を選択します。
1. **[+ 新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**[Apache Spark pool name]\(Apache Spark プール名\)**|`Spark1`
    |**ノード サイズ**| `Small`|
    |**[Number of nodes]\(ノードの数\)**| 最小数を 3 に、最大数を 3 に設定します|
    |||

1. **[確認および作成]** を選択し、次に **[作成]** を選択します。
1. Apache Spark プールの準備は数秒で完了します。

> [!NOTE]
> 名前に反して、Apache Spark プールは SQL プールに似ていません。 これは、Synapse ワークスペースに Spark との対話方法を通知するために使用する基本的なメタデータです。 

メタデータであるため、Spark プールを開始または停止することはできません。 

Synapse で Spark アクティビティを実行するときは、使用する Spark プールを指定します。 そのプールから Synapse に、使用する Spark リソースの数が通知されます。 使用したリソースに対してのみ支払います。 プールの使用を積極的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。

> [!NOTE]
> Spark データベースは Spark プールから独立して作成されます。 ワークスペースには、**既定**と呼ばれる Spark DB が必ず存在し、追加の Spark データベースを作成することができます。

## <a name="the-sql-on-demand-pool"></a>SQL オンデマンド プール

すべてのワークスペースには、**SQL オンデマンド**と呼ばれる事前に構築された削除できないプールが付属しています。 SQL オンデマンド プールを使用すると、Synapse SQL プールを作成したり、その管理を検討したりすることなく、SQL を操作できます。 他の種類のプールとは異なり、SQL オンデマンドの課金は、クエリを実行するためにスキャンされたデータの量に基づいて行われ、クエリの実行に使用されたリソースの数には基づきません。

* また、SQL オンデマンドには、SQL オンデマンド プールとは別に存在する独自の SQL オンデマンド データベースもあります。
* 現在、ワークスペースには、**SQL オンデマンド**という名前の SQL オンデマンド プールが 1 つだけあります。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>NYC タクシーのサンプル データを SQLDB1 データベースに読み込む

1. Synapse Studio で、一番上にある青いメニューの **[?]** アイコンを選択します。
1. **[作業の開始] > [作業の開始] ハブ**を選択します。
1. **[Query sample data]\(クエリ サンプル データ\)** というラベルの付いたカードで、`SQLDB1` という名前の SQL プールを選択します。
1. **[クエリ データ]** を選択します。 "Loading sample data (サンプルデータを読み込んでいます)" という通知が表示され、やがて消えます。
1. Synapse Studio の上部付近に、データを SQLDB1 に読み込み中であることを示す薄い青の通知バーが表示されます。 緑色になるまで待ってから、それを閉じます。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[SQLDB1] > [テーブル]** に移動します。 複数のテーブルが読み込まれていることがわかります。
1. **dbo.Trip** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\) > [Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
1. 新しい SQL スクリプトが作成され、自動的に実行されます。
1. SQL スクリプトの上部にある **[接続先]** が、自動的に SQLDB1 という SQL プールに設定されていることに注意してください。
1. SQL スクリプトのテキストをこのコードで置き換えて実行します。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更し、結果を折れ線グラフとして視覚的に表示します。

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのサンプル データを Spark nyctaxi データベースに読み込む

`SQLDB1` のテーブルで使用できるデータがあります。 次に、それを "nyctaxi" という名前の Spark データベースに読み込みます。

1. Synapse Studio で **[開発]** ハブに移動します。
1. **[+]** を選択し、 **[ノートブック]** を選択します。
1. ノートブックの上部にある **[アタッチ先]** の値を `Spark1` に設定します。
1. **[コードの追加]** を選択してノートブックのコード セルを追加し、次のテキストを貼り付けます。

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **[データ]** ハブに移動し、 **[データベース]** を右クリックして、 **[更新]** を選択します。
1. 次のデータベースが表示されます。
    - SQLDB (SQL プール)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. ノートブックに戻ります。
1. 新しいコード セルを作成し、次のテキストを入力して、`nyctaxi` Spark DB に読み込んだ NYC タクシーのデータの例に対してセルを実行します。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 次のコードを実行して、先ほど SQL プール `SQLDB1` で行ったのと同じ分析を実行します。 また、このコードでは、分析の結果を `nyctaxi.passengercountstats` という名前のテーブルに保存し、結果を視覚化します。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
    """) 
    display(df)
    df.write.saveAsTable("nyctaxi.passengercountstats")
    ```

1. セルの結果で **[グラフ]** を選択し、視覚化されたデータを確認します。
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Spark とノートブックを使用してデータ視覚化データをカスタマイズする

ノートブックを使用すると、グラフのレンダリング方法を制御できます。 次のコードは、一般的なライブラリ `matplotlib` と `seaborn` を使用した簡単な例を示しています。 先ほど SQL クエリを実行したときに表示されたのと同じ種類の折れ線グラフがレンダリングされます。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Spark テーブルから SQL プール テーブルにデータを読み込む

先ほど SQL プール テーブル `SQLDB1.dbo.Trip` から Spark テーブル `nyctaxi.trip` にデータをコピーしました。 次に、Spark を使用して、データを Spark テーブル `nyctaxi.passengercountstats` に集約しました。 次に、`nyctaxi.passengercountstats` のデータを `SQLDB1.dbo.PassengerCountStats` という名前の SQL プール テーブルにコピーします。 

ご自身のノートブックで以下のセルを実行します。 集約された Spark テーブルを SQL プール テーブルにコピーし直します。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL オンデマンドで使用して Spark データベース内の NYC タクシーのデータを分析する 

Spark データベース内のテーブルは、自動的に表示され、SQL オンデマンドによるクエリが可能です。

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します。
1. **[接続先]** を **[SQL on-demand]\(SQL オンデマンド\)** に設定します。 
1. スクリプトに次のテキストを貼り付けて、スクリプトを実行します。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > SQL オンデマンドを使用するクエリを初めて実行する場合、クエリの実行に必要な SQL リソースを SQL オンデマンドで収集するのに 10 秒ほどかかります。 それ以降のクエリでは、この時間は必要なく、大幅に高速になります。
  
## <a name="orchestrate-activities-with-pipelines"></a>パイプラインを使用してアクティビティを調整する

Azure Synapse では、さまざまなタスクを調整できます。 このセクションでは、それがどれだけ簡単であるかがわかります。

1. Synapse Studio で **[Orchestrate]\(調整\)** ハブに移動します。
1. **[+]** を選択し、次に **[パイプライン]** を選択します。 新しいパイプラインが作成されます。
1. [開発] ハブに移動し、先ほど作成したノートブックを見つけます。
1. そのノートブックをパイプラインにドラッグします。
1. パイプラインで、 **[トリガーの追加] > [新規作成/編集]** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、[繰り返し] でトリガーを 1 時間ごとに実行するように設定します。
1. **[OK]** を選択します。
1. **[すべて発行]** を選択すると、パイプラインが 1 時間ごとに実行されます。
1. 次の 1 時間を待機せずにパイプラインを今すぐ実行する場合は、 **[トリガーの追加] > [新規作成/編集]** を選択します。

## <a name="working-with-data-in-a-storage-account"></a>ストレージ アカウント内のデータを操作する

ここまでは、ワークスペースのデータベースにデータが存在するシナリオについて説明しました。 ここでは、ストレージ アカウント内のファイルを操作する方法について説明します。 このシナリオでは、ワークスペースの作成時に指定したワークスペースとコンテナーのプライマリ ストレージ アカウントを使用します。

* ストレージ アカウントの名前: `contosolake`
* ストレージ アカウント内のコンテナーの名前: `users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>ストレージ アカウントに CSV ファイルと Parquet ファイルを作成する

ノートブック内の次のコードを実行します。 ストレージ アカウントに CSV ファイルと parquet ファイルが作成されます。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>ストレージ アカウント内のデータを分析する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[リンク済み]** を選択します。
1. **[ストレージ アカウント] > [myworkspace (プライマリ - contosolake)]** に移動します。
1. **[users (プライマリ)]** を選択します。
1. "NYCTaxi" というフォルダーが表示されます。 その中に、"PassengerCountStats.csv" と "PassengerCountStats.parquet" という 2 つのフォルダーが表示されます。
1. "PassengerCountStats.parquet" フォルダーに移動します。
1. その中にある parquet ファイルを右クリックし、 **[新しいノートブック]** を選択すると、次のようなセルを含むノートブックが作成されます。

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. セルを実行します。
1. 中にある parquet ファイルを右クリックし、 **[New SQL script]\(新しい SQL スクリプト\) > [SELECT TOP 100 rows]\(上位 100 行の選択\)** を選択すると、次のような SQL スクリプトが作成されます。

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. このスクリプト内で、 **[アタッチ先]** フィールドが **[SQL on-demand]\(SQL オンデマンド\)** に設定されます。
1. スクリプトを実行します。

## <a name="visualize-data-with-power-bi"></a>Power BI でデータを視覚化する

NYX タクシーのデータから、2 つのテーブルに集約されたデータセットを作成しました。
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Power BI ワークスペースを Synapse ワークスペースにリンクすることができます。 これにより、Power BI ワークスペースに簡単にデータを取り込むことができます。また、Power BI レポートを Synapse ワークスペースで直接編集できます。

### <a name="create-a-power-bi-workspace"></a>Power BI ワークスペースを作成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) にサインインします。
1. `NYCTaxiWorkspace1` という名前の新しい Power BI ワークスペースを作成します。

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Synapse ワークスペースを新しい Power BI ワークスペースにリンクする

1. Synapse Studio で、 **[管理] > [リンクされたサービス]** に移動します。
1. **[+ 新規]** を選択し、 **[Power BI アカウントに接続する]** を選択して、次のフィールドを設定します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**名前**|`NYCTaxiWorkspace1`|
    |**ワークスペース名**|`NYCTaxiWorkspace1`|
    |||
    
1. **［作成］** を選択します

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Synapse ワークスペース内のデータを使用する Power BI データセットを作成する

1. Synapse Studio で、 **[開発] > [Power BI]** に移動します。
1. **[NYCTaxiWorkspace1] > [Power BI データセット]** に移動し、 **[New Power BI dataset]\(新しい Power BI データセット\)** を選択します。
1. `SQLDB1` データベースの上にマウス ポインターを移動し、 **[Download .pbids file]\(.pbids ファイルのダウンロード\)** を選択します。
1. ダウンロードした `.pbids` ファイルを開きます。 
1. これにより Power BI デスクトップが起動し、Synapse ワークスペース内の `SQLDB1` に自動的に接続されます。
1. **SQL Server データベース**というダイアログが表示された場合: a. **[Microsoft アカウント]** を選択します。 
    b. **[サインイン]** を選択してサインインします。
    c. **[接続]** を選択します。
1. **[ナビゲーター]** ダイアログが開きます。 その場合は、**PassengerCountStats** テーブルを確認し、 **[読み込み]** を選択します。
1. **[接続設定]** ダイアログが表示されます。 **[DirectQuery]** を選択し、 **[OK]** を選択します。
1. 左側の **[レポート]** ボタンを選択します。
1. レポートに**折れ線グラフ**を追加します。
    a. **PasssengerCount** 列を **[視覚エフェクト] > [軸]** にドラッグします。b. **SumTripDistance** 列と **AvgTripDistance** 列を **[視覚エフェクト] > [値]** にドラッグします。
1. **[ホーム]** タブで **[発行]** を選択します。
1. 変更を保存するかどうかの確認を求められます。 **[保存]** を選択します。
1. ファイル名を選択するように求めるメッセージが表示されます。 `PassengerAnalysis.pbix` を選択し、 **[保存]** を選択します。
1. **宛先を選択**するように求められるので、`NYCTaxiWorkspace1` を選択し、 **[選択]** を選択します。
1. 発行が完了するまで待ちます。

### <a name="configure-authentication-for-your-dataset"></a>データセットの認証を構成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) を開き、**サインイン**します。
1. 左側の **[ワークスペース]** で、`NYCTaxiWorkspace1` ワークスペースを選択します。
1. そのワークスペース内に、`Passenger Analysis` というデータセットと `Passenger Analysis` というレポートが表示されます。
1. `PassengerAnalysis` データセットの上にマウス ポインターを移動し、3 つのドットの付いたアイコンを選択して、 **[設定]** を選択します。
1. **[データ ソースの資格情報]** で、 **[認証方法]** を **[OAuth2]** に設定し、 **[サインイン]** を選択します。

### <a name="edit-a-report-in-synapse-studio"></a>Synapse Studio でレポートを編集する

1. Synapse Studio に戻り、 **[Close and refresh]\(閉じて更新\)** を選択します。 
1. **[開発]** ハブに移動します。 
1. **Power BI** の上にマウス ポインターを移動し、 **[Power BI レポート]** ノードの更新をクリックします。
1. **[Power BI]** の下に次のものが表示されます: a. * **[NYCTaxiWorkspace1] > [Power BI データセット]** の下に、**PassengerAnalysis** という新しいデータセット。
    b. * **[NYCTaxiWorkspace1] > [Power BI レポート]** の下に、**PassengerAnalysis** という新しいレポート。
1. **PassengerAnalysis** レポートを選択します。 
1. レポートが開き、Synapse Studio 内で直接レポートを編集できるようになります。

## <a name="monitor-activities"></a>モニター活動

1. Synapse Studio で、監視ハブに移動します。
1. この場所には、ワークスペースで発生したすべてのアクティビティの履歴と、現在アクティブになっているアクティビティが表示されます。
1. **[パイプラインの実行]** 、 **[Apache Spark applications]\(Apache Spark アプリケーション\)** 、 **[SQL requests]\(SQL 要求\)** を調べると、ワークスペースで既に実行した内容を確認することができます。

## <a name="next-steps"></a>次のステップ

[Azure Synapse Analytics (プレビュー)](overview-what-is.md) についての詳細情報

