---
title: チュートリアル:Azure Databricks を使用して抽出、読み込み、転送操作を実行する方法を学習する
description: このチュートリアルでは、Azure Data Lake Store Gen2 プレビューから Azure Databricks にデータを抽出し、そのデータを変換して、Azure SQL Data Warehouse に読み込む方法を学習します。
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107091"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う

このチュートリアルでは、Azure Databricks を使用して ETL (データの抽出、変換、読み込み) 操作を実行します。 Azure Data Lake Storage Gen2 が有効な Azure Storage アカウントから、Azure SQL Data Warehouse にデータを移動します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Databricks ワークスペースを作成する。
> * Azure Databricks で Spark クラスターを作成する。
> * Azure Data Lake Storage Gen2 対応のアカウントを作成する。
> * Azure Data Lake Storage Gen2 にデータをアップロードする。
> * Azure Databricks でノートブックを作成する。
> * Data Lake Storage Gen2 からデータを抽出する。
> * Azure Databricks でデータを変換する。
> * Azure SQL Data Warehouse にデータを読み込む。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure SQL データ ウェアハウスを作成し、サーバーレベルのファイアウォール規則を作成して、サーバー管理者としてサーバーに接続します。[Azure SQL データ ウェアハウスの作成のクイック スタート](../../sql-data-warehouse/create-data-warehouse-portal.md)に関する記事の手順に従ってください。
* Azure SQL データ ウェアハウスに使用するデータベース マスター キーを作成します。 「[データベース マスター キーの作成](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)」の記事の手順に従ってください。
* [Azure Data Lake Storage Gen2 アカウントを作成します](data-lake-storage-quickstart-create-account.md)。
* [U-SQL の例と問題追跡](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)のリポジトリから **small_radio_json.json** をダウンロードし、ファイルを保存したパスをメモしておきます。
* [Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-the-workspace"></a>ワークスペースを作成する

このセクションでは、Azure portal を使用して Azure Databricks ワークスペースを作成します。

1. Azure portal で、**[リソースの作成]** > **[分析]** > **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Azure Portal の Databricks")

1. **[Azure Databricks サービス]** で次の値を指定して、Databricks ワークスペースを作成します。

    |プロパティ  |[説明]  |
    |---------|---------|
    |**[ワークスペース名]**     | Databricks ワークスペースの名前を指定します。        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**[リソース グループ]**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**場所**     | **[米国西部 2]** を選択します。        |
    |**価格レベル**     |  **[Standard]** を選択します。     |

    ![Azure Databricks ワークスペースを作成する](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Azure Databricks ワークスペースを作成する")

1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、**[作成]** を選択します。

1. アカウントの作成には数分かかります。 アカウント作成時に、ポータルの右側に **[Azure Databricks のデプロイを送信しています]** タイルが表示されます。 操作の状態を監視するには、上部の進行状況バーを確認します。

    ![Databricks のデプロイのタイル](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Databricks のデプロイのタイル")

## <a name="create-the-spark-cluster"></a>Spark クラスターを作成する

このチュートリアルの操作を実行するには、Spark クラスターが必要です。 次の手順に従って、Spark クラスターを作成します。

1. Azure portal で、作成した Databricks ワークスペースに移動し、**[Launch Workspace]\(ワークスペースの起動\)** を選択します。

1. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** を選択します。

    ![Azure の Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Azure の Databricks")

1. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

1. 次のフィールドに値を入力し、他のフィールドの既定値はそのまま使用します。

    * クラスターの名前を入力します。
    * この記事では、**5.1** ランタイムを使用してクラスターを作成します。
    * **[Terminate after \_\_ minutes of inactivity]\(アクティビティが __ 分ない場合は終了する\)** チェック ボックスを必ずオンにします。 クラスターが使われていない場合は、クラスターを終了するまでの時間 (分単位) を指定します。

1. **[クラスターの作成]** を選択します。

クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

## <a name="create-a-file-system"></a>ファイル システムを作成する

Data Lake Storage Gen2 ストレージ アカウントにデータを格納するには、ファイル システムを作成する必要があります。

まず、Azure Databricks ワークスペースにノートブックを作成し、ストレージ アカウントにファイル システムを作成するコード スニペットを実行します。

1. [Azure portal](https://portal.azure.com) で、作成した Azure Databricks ワークスペースに移動し、**[Launch Workspace]\(ワークスペースの起動\)** を選択します。

1. 左側の **[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンで、**[作成]** > **[ノートブック]** の順に選択します。

    ![Databricks でノートブックを作成する](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Databricks でノートブックを作成する")

1. **[ノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。 言語として **[Scala]** を選んで、前に作成した Spark クラスターを選びます。

    ![Databricks でノートブックの詳細情報を入力する](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Databricks でノートブックの詳細情報を入力する")

    **作成**を選択します。

1. 次のコードをノートブックの最初のセルに入力し、そのコードを実行します。 サンプル内のブラケットで囲まれているプレースホルダーは、実際の値に置き換えてください。

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. Shift + Enter キーを押してコードを実行します。

これで、ストレージ アカウントのファイル システムが作成されます。

## <a name="upload-the-sample-data"></a>サンプル データのアップロード

次の手順では、後ほど Azure Databricks で変換するために、サンプル データ ファイルをストレージ アカウントにアップロードします。

ダウンロードしたサンプル データを自分のストレージ アカウントにアップロードします。 自分のストレージ アカウントにデータをアップロードする際に使用する方法は、階層型名前空間を有効にしているかどうかによって異なります。

アップロードを実行するには、Azure Data Factory、distp、または AzCopy (バージョン 10) を使用できます。 AzCopy バージョン 10 は、プレビュー版でのみ使用可能です。 AzCopy を使うには、次のコードをコマンド ウィンドウに貼り付けます。

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>データを抽出する

Databricks でサンプル データを使用するには、自分のストレージ アカウントからそのデータを抽出する必要があります。

Databricks ノートブックに戻り、ノートブックの新しいセルに次のコードを入力します。

空のコード セルに次のスニペットを追加します。 角かっこで囲まれているプレースホルダーは、先ほど保存したストレージ アカウントの値に置き換えてください。

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Shift + Enter キーを押してコードを実行します。

これで、サンプル json ファイルをデータフレームとして Azure Databricks に読み込むことができます。 新しいセルに次のコードを貼り付けます。 角かっこで囲まれているプレースホルダーは、実際の値に置き換えてください。

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Shift + Enter キーを押してコードを実行します。

次のコードを実行して、データ フレームの内容を表示します。

```scala
df.show()
```

次のスニペットのような出力が表示されます。

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

Azure Data Lake Storage Gen2 から Azure Databricks にデータが抽出されました。

## <a name="transform-the-data"></a>データの変換

未加工のサンプル データ ファイル **small_radio_json.json** は、ラジオ局のリスナー情報を収集したものであり、さまざまな列を含んでいます。 このセクションでは、このデータを変換して、データセットから特定の列だけを取得します。

まず、作成したデータフレームから、**firstName**、**lastName**、**gender**、**location**、**level** の各列だけを取得します。

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

次のスニペットに示されているような出力が得られます。

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

さらにこのデータを変換し、**level** 列の名前を **subscription_type** に変更することができます。

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

次のスニペットに示されているような出力が得られます。

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>データを読み込む

このセクションでは、変換したデータを Azure SQL Data Warehouse にアップロードします。 Azure Databricks 用の Azure SQL Data Warehouse コネクタを使用して、データフレームを SQL データ ウェアハウスのテーブルとして直接アップロードします。

SQL Data Warehouse コネクタは、Azure Blob Storage を一時ストレージとして使用し、Azure Databricks と Azure SQL Data Warehouse との間でデータをアップロードします。 それにはまず、そのストレージ アカウントに接続するための構成を指定します。 このアカウントは、この記事の前提条件としてあらかじめ作成しておく必要があります。

Azure Databricks から Azure Storage アカウントにアクセスするための構成を指定します。

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Azure Databricks と Azure SQL Data Warehouse の間でデータを移動するときに使用する一時フォルダーを指定します。

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

次のスニペットを実行して、Azure Blob Storage のアクセス キーを構成に格納します。 このアクションにより、アクセス キーをプレーンテキストのままノートブックに保持せずに済みます。

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Azure SQL Data Warehouse インスタンスに接続するための値を指定します。 SQL データ ウェアハウスは、前提条件としてあらかじめ作成しておく必要があります。

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

次のスニペットを実行して、変換済みのデータフレーム (**renamedColumnsDf**) をテーブルとして SQL データ ウェアハウスに読み込みます。 このスニペットは、SQL データベースに **SampleTable** というテーブルを作成します。

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

SQL データベースに接続し、**SampleTable** という名前のデータベースが表示されることを確認します。

![サンプル テーブルを確認する](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "サンプル テーブルを確認する")

選択クエリを実行して、テーブルの内容を確認します。 このテーブルには、**renamedColumnsDf** データフレームと同じデータが存在しているはずです。

![サンプル テーブルの内容を確認する](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "サンプル テーブルの内容を確認する")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルが完了したら、クラスターを終了できます。 Azure Databricks ワークスペースで、左側の **[クラスター]** を選択します。 クラスターが終了するように、**[アクション]** の下にある省略記号 (...) をポイントし、**終了**アイコンを選択します。

![Databricks クラスターを停止する](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスター作成時に **[Terminate after \_\_ minutes of inactivity]\(アクティビティが __ 分ない場合は終了する\)** チェック ボックスをオンにした場合は、手動で終了しなくても、クラスターは自動的に停止します。 このような場合、クラスターは、非アクティブな状態が一定の時間続くと自動的に停止します。

## <a name="next-steps"></a>次の手順

次のチュートリアルに進み、Azure Event Hubs を使ってリアルタイム データを Azure Databricks にストリーム配信する方法を学習してください。

> [!div class="nextstepaction"]
>[Event Hubs を使用してデータを Azure Databricks にストリーム配信する](../../azure-databricks/databricks-stream-from-eventhubs.md)
