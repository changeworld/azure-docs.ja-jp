---
title: チュートリアル:Azure Synapse Analytics の使用を開始する
description: このチュートリアルでは、Azure Synapse Analytics を設定して使用する基本的な手順について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791862"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics の使用を開始する

このチュートリアルでは、Azure Synapse Analytics を設定して使用する基本的な手順について説明します。

## <a name="prepare-a-storage-account"></a>ストレージ アカウントを準備する

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 次の設定で新しいストレージ アカウントを作成します。

    |タブ|設定 | 推奨値 | 説明 |
    |---|---|---|---|
    |基本|**Storage account name \(ストレージ アカウント名\)**| 任意の名前を付けることができます。|このドキュメントでは、**contosolake** とします。|
    |基本|**アカウントの種類**|**StorageV2** に設定する必要があります。||
    |基本|**場所**|任意の場所を選択します。| Azure Synapse Analytics ワークスペースと Azure Data Lake Storage Gen2 アカウントは同じリージョンに配置することをお勧めします。|
    |詳細設定|**Data Lake Storage Gen2**|**有効**| Azure Synapse は、この設定が有効になっているストレージ アカウントでのみ機能します。|
    |||||

1. ストレージ アカウントの作成後、左ペインの **[アクセス制御 (IAM)]** を選択します。 次に、以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。
    1. **所有者**ロールに自分自身を割り当てます。
    1. **ストレージ BLOB データ所有者**ロールに自分自身を割り当てます。
1. 左ペインで **[コンテナー]** を選択し、コンテナーを作成します。
1. コンテナーには任意の名前を付けることができます。 このドキュメントでは、このコンテナーに **users** という名前を付けます。
1. 既定の設定である **[パブリック アクセス レベル]** をそのまま使用し、 **[作成]** を選択します。

次の手順では、このストレージ アカウントを "プライマリ" ストレージ アカウントとして使用し、コンテナーをワークスペースの格納に使用するよう Azure Synapse ワークスペースを構成します。 このワークスペースでは、データが Apache Spark テーブルに格納されます。 **/synapse/workspacename** というフォルダーに Spark アプリケーション ログが格納されます。

## <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で **Synapse** を検索します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics (ワークスペース プレビュー)]** を選択します。
1. **[追加]** を選択し、これらの設定を使用してワークスペースを作成します。

    |タブ|設定 | 推奨値 | 説明 |
    |---|---|---|---|
    |基本|**ワークスペース名**|任意の名前にすることができます。| このドキュメントでは、**myworkspace** を使用します。|
    |基本|**リージョン**|ストレージ アカウントのリージョンに一致させます。|

1. **[Data Lake Storage Gen 2]** で、先ほど作成したアカウントとコンテナーを選択します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

## <a name="verify-access-to-the-storage-account"></a>ストレージ アカウントへのアクセスを確認する

Azure Synapse ワークスペースのマネージド ID には、既にストレージ アカウントへのアクセス権がある可能性があります。 確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、ワークスペース用に選択したプライマリ ストレージ アカウントを開きます。
1. 左ペインから **[アクセス制御 (IAM)]** を選択します。
1. 以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。 ここでは、ワークスペース ID とワークスペース名に同じ名前を使用します。
    1. ストレージ アカウントの **[ストレージ BLOB データ共同作成者]** ロールに、ワークスペース ID として **myworkspace** を割り当てます。
    1. ワークスペース名として **myworkspace** を割り当てます。

1. **[保存]** を選択します。

## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開きます。 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* https://web.azuresynapse.net にアクセスし、ワークスペースにサインインします。

## <a name="create-a-sql-pool"></a>SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**SQL プール名**| **SQLDB1**|
    |**パフォーマンス レベル**|**DW100C**|
    |||

1. **[確認と作成]**  >  **[作成]** の順に選択します。 SQL プールの準備は数分で完了します。 SQL プールは、**SQLDB1** とも呼ばれる SQL プール データベースに関連付けられます。

SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

## <a name="create-an-apache-spark-pool"></a>Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**[Apache Spark pool name]\(Apache Spark プール名\)**|**Spark1**
    |**ノード サイズ**| **Small**|
    |**[Number of nodes]\(ノードの数\)**| 最小数を 3 に、最大数を 3 に設定します|

1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

> [!NOTE]
> 名前に反して、Apache Spark プールは SQL プールに似ていません。 これは、Azure Synapse ワークスペースに対して Spark との対話方法を指示するために使用する基本的なメタデータにすぎません。

メタデータであるため、Spark プールを開始したり停止したりすることはできません。

Azure Synapse で Spark アクティビティを実行する際は、使用する Spark プールを指定します。 そのプールから Azure Synapse に対して、使用する Spark リソースの数が指示されます。 支払いの対象となるのは、使用したリソースの分だけです。 プールの使用を能動的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。

> [!NOTE]
> Spark データベースは Spark プールから独立して作成されます。 ワークスペースには必ず、**既定**と呼ばれる Spark データベースが存在します。 追加の Spark データベースを作成することができます。

## <a name="the-sql-on-demand-pool"></a>SQL オンデマンド プール

各ワークスペースには、**SQL オンデマンド**というあらかじめ構築されたプールが付属しています。 このプールを削除することはできません。 SQL オンデマンド プールを使用すると、Azure Synapse で SQL プールを作成したり、その管理を検討したりすることなく、SQL を操作できます。

他の種類のプールとは異なり、SQL オンデマンドの課金は、クエリの実行に使用されたリソースの数ではなく、クエリを実行するためにスキャンされたデータの量に基づきます。

* SQL オンデマンドには、SQL オンデマンド プールとは別に存在する独自の SQL オンデマンド データベースもあります。
* ワークスペースには必ず、**SQL オンデマンド**という名前の SQL オンデマンド プールが 1 つだけあります。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>NYC タクシーのサンプル データを SQLDB1 データベースに読み込む

1. Synapse Studio で、一番上にある青いメニューの **[?]** アイコンを選択します。
1. **[作業の開始]**  >  **[作業の開始] ハブ**を選択します。
1. **[Query sample data]\(クエリ サンプル データ\)** というラベルの付いたカードで、**SQLDB1** という名前の SQL プールを選択します。
1. **[クエリ データ]** を選択します。 "Loading sample data" (サンプル データを読み込んでいます) という通知が少しの間表示されます。 Synapse Studio の上部付近にある薄い青のステータス バーで、データを SQLDB1 に読み込み中であることが示されます。
1. ステータス バーが緑色に変わったら、それを閉じます。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[SQLDB1]**  >  **[テーブル]** に移動します。 複数のテーブルが読み込まれていることがわかります。
1. **dbo.Trip** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\)**  >  **[Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
1. 新しい SQL スクリプトが作成されて実行されるまで待ちます。
1. SQL スクリプトの上部にある **[接続先]** が自動的に **SQLDB1** という SQL プールに注目します。
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

    このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更して、結果を折れ線グラフとして視覚的に表示します。

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのデータを Spark nyctaxi データベースに読み込む

**SQLDB1** のテーブルで使用できるデータがあります。 それを **nyctaxi** という名前の Spark データベースに読み込みます。

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. **[+]**  >  **[ノートブック]** を選択します。
1. ノートブックの上部にある **[アタッチ先]** の値を **[Spark1]** に設定します。
1. **[コードの追加]** を選択してノートブックのコード セルを追加し、次のテキストを貼り付けます。

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **[データ]** ハブに移動し、 **[データベース]** を右クリックして、 **[更新]** を選択します。 次のデータベースが表示されます。

    - **SQLDB1** (SQL プール)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. ノートブックに戻ります。
1. 新しいコード セルを作成し、次のテキストを入力します。 その後、このセルを実行して、**nyctaxi** Spark データベースに読み込んだ NYC Taxi データを表示します。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 次のコードを実行して、先ほど SQL プール **SQLDB1** で行ったのと同じ分析を実行します。 このコードにより、分析の結果が **nyctaxi.passengercountstats** という名前のテーブルに保存され、結果が視覚化されます。

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

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Spark とノートブックを使用してデータの視覚化をカスタマイズする

グラフのレンダリング方法は、ノートブックを使用して制御できます。 次のコードでは、簡単な例を示します。 一般的なライブラリである **matplotlib** と **seaborn** が使用されています。 先ほど実行した SQL クエリと同じ種類の折れ線グラフがレンダリングされます。

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

先ほど SQL プール テーブル **SQLDB1.dbo.Trip** のデータを Spark テーブル **nyctaxi.trip** にコピーしました。 次に、Spark を使用して、データを Spark テーブル **nyctaxi.passengercountstats** に集約しました。 今度は、**nyctaxi.passengercountstats** のデータを **SQLDB1.dbo.PassengerCountStats** という名前の SQL プール テーブルにコピーします。

ノートブックで次のセルを実行します。 集約された Spark テーブルが再度 SQL プール テーブルにコピーされます。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL オンデマンドを使用して Spark データベース内の NYC タクシーのデータを分析する

Spark データベース内のテーブルは自動的に表示され、SQL オンデマンドによるクエリが可能です。

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します。
1. **[接続先]** を **[SQL on-demand]\(SQL オンデマンド\)** に設定します。
1. スクリプトに次のテキストを貼り付けて、スクリプトを実行します。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > SQL オンデマンドを使用するクエリを初めて実行する場合は、クエリの実行に必要な SQL リソースを SQL オンデマンドで収集するのに 10 秒ほどかかります。 それ以降のクエリの実行時間は大幅に短縮されます。
  
## <a name="orchestrate-activities-with-pipelines"></a>パイプラインを使用してアクティビティを調整する

Azure Synapse では、さまざまなタスクを調整できます。

1. Synapse Studio で **[Orchestrate]\(調整\)** ハブに移動します。
1. **[+]**  >  **[パイプライン]** を選択して新しいパイプラインを作成します。
1. **[開発]** ハブに移動し、先ほど作成したノートブックを見つけます。
1. そのノートブックをパイプラインにドラッグします。
1. パイプラインで、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、 **[繰り返し]** でトリガーを 1 時間ごとに実行するよう設定します。
1. **[OK]** を選択します。
1. **[すべて公開]** を選択します。 パイプラインが 1 時間おきに実行されます。
1. 1 時間待たずに今すぐパイプラインを実行する場合は、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。

## <a name="work-with-data-in-a-storage-account"></a>ストレージ アカウント内のデータを操作する

ここまでは、データがワークスペース内のデータベースに存在するシナリオについて説明してきました。 次に、ストレージ アカウント内のファイルを操作する方法について説明します。 このシナリオでは、ワークスペースの作成時に指定したワークスペースとコンテナーのプライマリ ストレージ アカウントを使用します。

* ストレージ アカウントの名前: **contosolake**
* ストレージ アカウント内のコンテナーの名前: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>ストレージ アカウントに CSV および Parquet ファイルを作成する

ノートブック内の次のコードを実行します。 ストレージ アカウントに CSV ファイルと parquet ファイルが作成されます。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>ストレージ アカウント内のデータを分析する

1. Synapse Studio の **[データ]** ハブに移動し、 **[Linked]\(リンク済み\)** を選択します。
1. **[ストレージ アカウント]**  >  **[myworkspace (プライマリ - contosolake)]** に移動します。
1. **[users (プライマリ)]** を選択します。 **NYCTaxi** フォルダーが表示されます。 その中に、**PassengerCountStats.csv** と **PassengerCountStats.parquet** という 2 つのフォルダーが表示されます。
1. **PassengerCountStats.parquet** フォルダーを開きます。 その中に、*part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet* のような名前の Parquet ファイルが表示されます。
1. **.parquet** を右クリックし、 **[新しいノートブック]** を選択します。 次のようなセルを含んだノートブックが作成されます。

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. セルを実行します。
1. Parquet ファイル内を右クリックして、 **[New SQL script]\(新しい SQL スクリプト\)**  >  **[SELECT TOP 100 rows]\(上位 100 行の選択\)** を選択します。 次のような SQL スクリプトが作成されます。

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     このスクリプトでは、 **[アタッチ先]** フィールドが **[SQL on-demand]\(SQL オンデマンド\)** に設定されます。

1. スクリプトを実行します。

## <a name="visualize-data-with-power-bi"></a>Power BI でデータを視覚化する

NYC タクシーのデータから、2 つのテーブルに集約されたデータセットを作成しました。
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Power BI ワークスペースを Azure Synapse ワークスペースにリンクすることができます。 これにより、Power BI ワークスペースに簡単にデータを取り込むことができます。 Power BI レポートは、Azure Synapse ワークスペースで直接編集できます。

### <a name="create-a-power-bi-workspace"></a>Power BI ワークスペースを作成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) にサインインします。
1. **NYCTaxiWorkspace1** という名前の新しい Power BI ワークスペースを作成します。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Azure Synapse ワークスペースを新しい Power BI ワークスペースにリンクする

1. Synapse Studio で、 **[管理]**  >  **[リンクされたサービス]** に移動します。
1. **[新規]**  >  **[Power BI アカウントに接続する]** を選択して、次のフィールドを選択します。

    |設定 | 推奨値 | 
    |---|---|
    |**名前**|**NYCTaxiWorkspace1**|
    |**ワークスペース名**|**NYCTaxiWorkspace1**|

1. **［作成］** を選択します

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Azure Synapse ワークスペース内のデータを使用する Power BI データセットを作成する

1. Synapse Studio で、 **[開発]**  >  **[Power BI]** に移動します。
1. **[NYCTaxiWorkspace1]**  >  **[Power BI データセット]** に移動し、 **[New Power BI dataset]\(新しい Power BI データセット\)** を選択します。
1. **SQLDB1** データベースの上にマウス ポインターを移動し、 **[Download .pbids file]\(.pbids ファイルのダウンロード\)** を選択します。
1. ダウンロードした **.pbids** ファイルを開きます。 Power BI Desktop が開いて、Azure Synapse ワークスペース内の **SQLDB1** に自動的に接続します。
1. **[SQL Server データベース]** というダイアログ ボックスが表示された場合は、次の手順を実行します。
    1. **[Microsoft アカウント]** を選択します。
    1. **[サインイン]** を選択してアカウントにサインインします。
    1. **[接続]** を選択します。
1. **[ナビゲーター]** ダイアログ ボックスが開いたら、**PassengerCountStats** テーブルをチェックし、 **[読み込み]** を選択します。
1. **[接続の設定]** ダイアログ ボックスが表示されたら、 **[DirectQuery]**  >  **[OK]** をクリックします。
1. 左側の **[レポート]** ボタンを選択します。
1. レポートに**折れ線グラフ**を追加します。
    1. **[PassengerCount]** 列を **[視覚化]**  >  **[軸]** にドラッグします。
    1. **SumTripDistance** 列と **AvgTripDistance** 列を **[視覚化]**  >  **[値]** にドラッグします。
1. **[ホーム]** タブで **[発行]** を選択します。
1. **[保存]** を選択して変更を保存します。
1. **PassengerAnalysis.pbix** というファイル名を選択し、 **[保存]** を選択します。
1. **[宛先を選択してください]** で **[NYCTaxiWorkspace1]** を選択し、 **[選択]** をクリックします。
1. 発行が完了するまで待ちます。

### <a name="configure-authentication-for-your-dataset"></a>データセットの認証を構成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) を開き、**サインイン**します。
1. 左側の **[ワークスペース]** で、**NYCTaxiWorkspace1** ワークスペースを選択します。
1. ワークスペース内で、**Passenger Analysis** というデータセットと **Passenger Analysis** というレポートを探します。
1. **PassengerAnalysis** データセットにマウス ポインターを合わせ、省略記号 (...) ボタンを選択して、 **[設定]** を選択します。
1. **[データ ソースの資格情報]** で、 **[認証方法]** を **[OAuth2]** に設定し、 **[サインイン]** を選択します。

### <a name="edit-a-report-in-synapse-studio"></a>Synapse Studio でレポートを編集する

1. Synapse Studio に戻り、 **[Close and refresh]\(閉じて更新\)** を選択します。
1. **[開発]** ハブに移動します。
1. **Power BI** の上にマウス ポインターを移動し、 **[Power BI レポート]** ノードの更新を選択します。
1. **[Power BI]** に次のものが表示されます。
    1. **[NYCTaxiWorkspace1]**  >  **[Power BI データセット]** に、**PassengerAnalysis** という新しいデータセットが表示されます。
    1. **[NYCTaxiWorkspace1]**  >  **[Power BI レポート]** に、**PassengerAnalysis** という新しいレポートが表示されます。
1. **PassengerAnalysis** レポートを選択します。 レポートが開き、Synapse Studio 内で直接レポートを編集することができます。

## <a name="monitor-activities"></a>モニター活動

1. Synapse Studio で **[監視]** ハブに移動します。
1. この場所には、ワークスペースで発生したすべてのアクティビティの履歴と、現在アクティブになっているアクティビティが表示されます。
1. **[パイプラインの実行]** 、 **[Apache Spark applications]\(Apache Spark アプリケーション\)** 、 **[SQL requests]\(SQL 要求\)** を調べ、ワークスペースで既に実行した内容を確認します。

## <a name="next-steps"></a>次のステップ

[Azure Synapse Analytics (ワークスペース プレビュー)](overview-what-is.md) の詳細を確認する。

