---
title: Spark を使用して Azure Databricks で Azure Data Lake Storage Gen2 プレビューのデータにアクセスする | Microsoft Docs
description: Azure Databricks クラスター上で Spark クエリを実行して、Azure Data Lake Storage Gen2 ストレージ アカウント内のデータにアクセスする方法を説明します。
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 42bf3fca0e30c21e5f8737e37f4ac35db188ee90
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616492"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>チュートリアル: Spark を使用して Azure Databricks で Azure Data Lake Storage Gen2 プレビューのデータにアクセスする

このチュートリアルでは、Azure Databricks クラスター上で Spark クエリを実行して、Azure Data Lake Storage Gen2 プレビュー対応アカウント内のデータを照会する方法を説明します。

> [!div class="checklist"]
> * Databricks クラスターを作成する
> * 非構造化データをストレージ アカウントに取り込む
> * データを処理する Azure 関数をトリガーする
> * Blob Storage 内でデータ分析を実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、航空便の定時運航データ ([米国運輸省](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time)で入手できます) の使用方法と照会方法を示します。 少なくとも 2 年間分の航空会社のデータをダウンロードし (すべてのフィールドを選択します)、その結果を、ご使用のマシンに保存します。 ダウンロードしたファイル名とパスを必ずメモしておいてください。この情報は後の手順で必要になります。

> [!NOTE]
> **[Prezipped file]\(事前に圧縮されたファイル\)** チェックボックスをオンにして、すべてのデータ フィールドを選択します。 ダウンロード サイズは数ギガバイトにもなりますが、分析にはこの量のデータが必要です。

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 アカウントを作成する

開始するには、新しい [Azure Data Lake Storage Gen2 アカウント](quickstart-create-account.md)を作成し、一意の名前を指定します。 ストレージ アカウントに移動し、構成設定を取得します。

> [!IMPORTANT]
> プレビュー中は、Azure Functions は、フラットな名前空間で作成された Azure Data Lake Storage Gen2 アカウントのみで機能します。

1. **[設定]** で **[アクセス キー]** をクリックします。
3. **[key1]** の隣にある **[コピー]** をクリックしてキーの値をコピーします。

このチュートリアルの後の手順で、アカウント名とキーの両方が必要になります。 テキスト エディターを開き、後で参照できるようにアカウント名とキーをメモしておきます。

## <a name="create-a-databricks-cluster"></a>Databricks クラスターを作成する

次の手順では、[Databricks クラスター](https://docs.azuredatabricks.net/)を作成して、データ ワークスペースを作成します。

1. [Databricks サービス](https://ms.portal.azure.com/#create/Microsoft.Databricks)を作成し、**myFlightDataService** という名前を付けます (このサービスを作成するときに、*[ダッシュボードにピン留めする]* チェック ボックスを必ずオンにします)。
2. **[Launch Workspace]\(ワークスペースの起動\)** をクリックして、新しいブラウザー ウィンドウでワークスペースを開きます。
3. 左側のナビゲーション バーで **[クラスター]** をクリックします。
4. **[クラスターの作成]** をクリックします。
5. 「**myFlightDataCluster**」を *[クラスター名]* フィールドに入力します。
6. *[ワーカー タイプ]* フィールドで **[Standard_D8s_v3]** を選択します。
7. **[Min Workers]\(最小ワーカー\)** の値を *4* に変更します。
8. ページの上部にある **[クラスターの作成]** をクリックします (このプロセスは完了までに最大 5 分かかる場合があります)。
9. 処理が完了したら、ナビゲーション バーの左上にある **[Azure Databricks]** を選択します。
10. ページの下半分にある **[新規]** セクションで **[Notebook]\(ノートブック\)** を選択します。
11. **[名前]** フィールドに任意の名前を入力し、言語として **[Python]** を選択します。
12. その他のフィールドはすべて既定値のままでかまいません。
13. **作成**を選択します。
14. **[Cmd 1]** セルに次のコードを貼り付けて、値をストレージ アカウントに保持していた値で置き換えます。

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>データの取り込み

### <a name="copy-source-data-into-the-storage-account"></a>ソース データをストレージ アカウントにコピーする

次のタスクでは、AzCopy を使用して、*.csv* ファイルから Azure storage にデータをコピーします。 コマンド プロンプト ウィンドウを開き、次のコマンドを入力します。 `<DOWNLOAD_FILE_PATH>`、`<ACCOUNT_NAME>`、および `<ACCOUNT_KEY>` の各プレースホルダーを、前の手順でメモしておいた対応する値で必ず置き換えてください。

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
6. **[Cmd 1]** セルに次のコードを貼り付けます (このコードはエディターに自動保存されます)。

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Hadoop 分散ファイル システムを使用してデータを調べる

Databricks ワークスペースに戻り、左側のナビゲーション バーにある **[Recent]\(最近使った項目\)** アイコンをクリックします。

1. **[Flight Data Analytics]** ノートブックをクリックします。
2. **Ctrl + Alt + N** キーを押して新しいセルを作成します。

次のコード ブロックをそれぞれ **[Cmd 1]** に入力し、**Cmd を押しながら Enter** キーを押して Python スクリプトを実行します。

AzCopy を使用してアップロードした CSV ファイルの一覧を取得するには、次のスクリプトを実行します。

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

新しいファイルを作成して *parquet/flights* フォルダー内のファイルの一覧を作成するには、次のスクリプトを実行します。

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
上記のコード サンプルを使用して、Azure Data Lake Storage Gen2 対応アカウントの格納データを使って HDFS の階層的な性質を調査しました。

## <a name="query-the-data"></a>データを照会する

これで、Azure Data Lake Storage にアップロードしたデータの照会を開始できます。 次のコード ブロックをそれぞれ **[Cmd 1]** に入力し、**Cmd を押しながら Enter** キーを押して Python スクリプトを実行します。

### <a name="simple-queries"></a>単純なクエリ

データ ソースのデータフレームを作成するには、次のスクリプトを実行します。

> [!IMPORTANT]
> **<YOUR_CSV_FILE_NAME>** プレースホルダーを、このチュートリアルの始めにダウンロードしたファイル名で必ず置き換えてください。

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

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
#preferrably run this in a separate cmd cell
display(flightDF)
```

データに対して分析クエリを実行するには、次のスクリプトを実行します。

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
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
### <a name="complex-queries"></a>複雑なクエリ

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

## <a name="next-steps"></a>次の手順

* [Azure HDInsight の Apache Hive を使用してデータの抽出、変換、読み込みを行う](tutorial-extract-transform-load-hive.md)