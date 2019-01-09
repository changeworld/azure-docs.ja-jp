---
title: チュートリアル:Spark を使用して Azure Databricks で Azure Data Lake Storage Gen2 プレビューのデータにアクセスする | Microsoft Docs
description: このチュートリアルでは、Azure Databricks クラスター上で Spark クエリを実行して、Azure Data Lake Storage Gen2 ストレージ アカウント内のデータにアクセスする方法を説明します。
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: b0382d31f9d16228ca3447ace9c7d4f171b206f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548988"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>チュートリアル:Spark を使用して Azure Databricks で Data Lake Storage Gen2 プレビューのデータにアクセスする

このチュートリアルでは、Azure Data Lake Storage Gen2 (プレビュー) 対応の Azure Storage アカウントにある格納データに Azure Databricks クラスターを接続する方法を説明します。 この接続を使用することで、必要なデータに関するクエリや分析をクラスターからネイティブに実行することができます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Databricks クラスターを作成する
> * 非構造化データをストレージ アカウントに取り込む
> * Blob Storage 内でデータ分析を実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、航空便の定時運航データ ([米国運輸省](https://transtats.bts.gov/DL_SelectFields.asp)で入手できます) の使用方法と照会方法を示します。 

1. **[Prezipped file]\(事前に圧縮されたファイル\)** チェックボックスをオンにして、すべてのデータ フィールドを選択します。
2. **[ダウンロード]** を選択して、ご使用のマシンに結果を保存します。
3. ダウンロードしたファイルの名前とパスは必ずメモしておいてください。この情報は後の手順で必要になります。

このチュートリアルを完了するには、分析機能を含んだストレージ アカウントが必要です。 その作成方法に関する[クイック スタート](data-lake-storage-quickstart-create-account.md)を済ませておくことをお勧めします。 作成したら、そのストレージ アカウントに移動して構成設定を取得してください。

1. **[設定]** で **[アクセス キー]** を選択します。
2. **[key1]** の隣にある **[コピー]** を選択してキーの値をコピーします。

このチュートリアルの後の手順で、アカウント名とキーの両方が必要になります。 テキスト エディターを開き、後で参照できるようにアカウント名とキーをメモしておきます。

## <a name="create-a-databricks-cluster"></a>Databricks クラスターを作成する

次の手順では、Databricks クラスターを作成して、データ ワークスペースを作成します。

1. [Azure portal](https://portal.azure.com) から **[リソースの作成]** を選択します。
2. 検索フィールドに「**Azure Databricks**」と入力します。
3. [Azure Databricks] ブレードの **[作成]** を選択します。
4. Databricks サービスに **myFlightDataService** という名前を付けます (このサービスを作成するときに、*[ダッシュボードにピン留めする]* チェック ボックスを必ずオンにします)。
5. **[Launch Workspace]\(ワークスペースの起動\)** を選択して、新しいブラウザー ウィンドウでワークスペースを開きます。
6. 左側のナビゲーション バーで **[クラスター]** を選択します。
7. **[クラスターの作成]** を選択します。
8. 「**myFlightDataCluster**」を **[クラスター名]** フィールドに入力します。
9. **[ワーカー タイプ]** フィールドで **[Standard_D8s_v3]** を選択します。
10. **[Min Workers]\(最小ワーカー\)** の値を **4** に変更します。
11. ページ上部の **[クラスターの作成]** を選択します。 この処理は、完了までに 5 分ほどかかることがあります。
12. 処理が完了したら、ナビゲーション バーの左上にある **[Azure Databricks]** を選択します。
13. ページの下半分にある **[新規]** セクションで **[Notebook]\(ノートブック\)** を選択します。
14. **[名前]** フィールドに任意の名前を入力し、言語として **[Python]** を選択します。
15. その他のフィールドはすべて既定値のままでかまいません。
16. **作成**を選択します。
17. 次のコードを **Cmd 1** セルに貼り付けます。 サンプル内のブラケットで囲まれているプレースホルダーは、実際の値に置き換えてください。

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
18. **Shift + Enter** キーを押して、コード セルを実行します。

## <a name="ingest-data"></a>データの取り込み

### <a name="copy-source-data-into-the-storage-account"></a>ソース データをストレージ アカウントにコピーする

次のタスクでは、AzCopy を使用して、*.csv* ファイルから Azure storage にデータをコピーします。 コマンド プロンプト ウィンドウを開き、次のコマンドを入力します。 `<DOWNLOAD_FILE_PATH>`、`<ACCOUNT_NAME>`、`<ACCOUNT_KEY>` の各プレースホルダーを、前の手順でメモしておいた対応する値で必ず置き換えてください。

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Notebook を使用して CSV を Parquet に変換する

お使いのブラウザーで Databricks を再度開き、次の手順を実行します。

1. ナビゲーション バーの左上にある **[Azure Databricks]** を選択します。
2. ページの下半分にある **[新規]** セクションで **[Notebook]\(ノートブック\)** を選択します。
3. **[名前]** フィールドに「**CSV2Parquet**」と入力します。
4. その他のフィールドはすべて既定値のままでかまいません。
5. **作成**を選択します。
6. 次のコードを **Cmd 1** セルに貼り付けます。 このコードはエディターに自動保存されます。

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>データを調査する

Databricks ワークスペースに戻り、左側のナビゲーション バーにある **[Recent]\(最近使った項目\)** アイコンを選択します。

1. **[Flight Data Analytics]** ノートブックを選択します。
2. **Ctrl + Alt + N** キーを押して新しいセルを作成します。

次のコード ブロックをそれぞれ **[Cmd 1]** に入力し、**Cmd を押しながら Enter** キーを押して Python スクリプトを実行します。

AzCopy を使用してアップロードした CSV ファイルの一覧を取得するには、次のスクリプトを実行します。

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

新しいファイルを作成して *parquet/flights* フォルダー内のファイルの一覧を作成するには、次のスクリプトを実行します。

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

上記のコード サンプルでは、Data Lake Storage Gen2 対応のストレージ アカウントに格納されたデータを使って HDFS の階層的な性質を調査しました。

## <a name="query-the-data"></a>データを照会する

これで、ストレージ アカウントにアップロードしたデータの照会を開始できます。 次のコード ブロックをそれぞれ **[Cmd 1]** に入力し、**Cmd を押しながら Enter** キーを押して Python スクリプトを実行します。

### <a name="run-simple-queries"></a>単純なクエリを実行する

データ ソースのデータフレームを作成するには、次のスクリプトを実行します。

> [!IMPORTANT]
> **<YOUR_CSV_FILE_NAME>** プレースホルダーを、このチュートリアルの始めにダウンロードしたファイル名で必ず置き換えてください。

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

データに対して分析クエリを実行するには、次のスクリプトを実行します。

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>複雑なクエリを実行する

より複雑な以下のクエリを実行するには、ノートブックの各セグメントに対して同時に実行して結果を確認します。

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。 これを行うには、ストレージ アカウントのリソース グループを選択し、**[削除]** を選択してください。

## <a name="next-steps"></a>次の手順

[!div class="nextstepaction"] 
> [Azure HDInsight の Apache Hive を使用してデータの抽出、変換、読み込みを行う](data-lake-storage-tutorial-extract-transform-load-hive.md)

