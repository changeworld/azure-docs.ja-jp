---
title: チュートリアル:Azure HDInsight の Spark コンピューティング コンテキストで R を使用する
description: チュートリアル - ML Services で R と Spark を使用してみます。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451743"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight の Spark コンピューティング コンテキストで R を使用する

このチュートリアルでは、Azure HDInsight の ML Services クラスター上で実行されている Apache Spark で R 関数を使用するための詳しい手順を示します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ローカル ストレージにサンプル データをダウンロードする
> * 既定のストレージにデータをコピーする
> * データ セットを設定する
> * データ ソースの作成
> * Spark のコンピューティング コンテキストを作成する
> * 線形モデルを適合させる
> * 複合 XDF ファイルを使用する
> * XDF から CSV に変換する

## <a name="prerequisites"></a>前提条件

* HDInsight 上の ML Services クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、 **[クラスターの種類]** で **[ML Services]** を選択してください。

## <a name="connect-to-rstudio-server"></a>RStudio Server に接続する

RStudio Server は、クラスターのエッジ ノード上で実行されます。 次の URL に移動します (`CLUSTERNAME` は、作成した ML Services クラスターの名前)。

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

初めてサインインするときは認証を 2 回行う必要があります。 最初の認証プロンプトでは、クラスター管理者のログインとパスワードを指定します。既定では `admin` です。 2 つ目の認証プロンプトでは、SSH ログインとパスワードを指定します。既定では `sshuser` です。 以降のサインインでは、SSH 資格情報のみが求められます。

## <a name="download-sample-data"></a>サンプル データをダウンロードする

*Airline 2012 On-Time Data Set* は、12 個のコンマ区切りファイルで構成されており、各ファイルには 2012 年の米国内の全民間飛行便の発着情報が入っています。 これは、600 万件を超える観察データが含まれるビッグ データ セットです。

1. いくつかの環境変数を初期化します。 RStudio Server コンソールに以下のコードを入力します。

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    変数は、画面の右側の **[環境]** タブに表示されます。

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  ローカル ディレクトリを作成し、サンプル データをダウンロードします。 RStudio に次のコードを入力します。

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    ダウンロードは約 9 分半で完了します。

## <a name="copy-data-to-default-storage"></a>既定のストレージにデータをコピーする

HDFS の場所は、`airDataDir` 変数で指定します。 RStudio に次のコードを入力します。

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

この手順は、約 10 秒で完了します。

## <a name="set-up-data-set"></a>データ セットを設定する

1. 既定値を使用するファイル システム オブジェクトを作成します。 RStudio に次のコードを入力します。

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. 元の CSV ファイルにはかなり扱いにくい変数名が付いているので、管理しやすくするために `colInfo` という一覧を用意しました。 RStudio に次のコードを入力します。

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-source"></a>データ ソースの作成

Spark コンピューティング コンテキストでは、以下の関数を使用してデータ ソースを作成できます。

|Function | 説明 |
|---------|-------------|
|`RxTextData` | コンマ区切りのテキスト データ ソース。 |
|`RxXdfData` | XDF データ ファイル形式のデータ。 RevoScaleR では、1 つのファイルではなく、ファイルの複合セットにデータを格納するために、XDF ファイル形式が Hadoop 用に変更されます。 |
|`RxHiveData` | Hive データ ソース オブジェクトを生成します。|
|`RxParquetData` | Parquet データ ソース オブジェクトを生成します。|
|`RxOrcData` | Orc データ ソース オブジェクトを生成します。|

HDFS にコピーしたファイルを使用して、[RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) オブジェクトを作成します。 RStudio に次のコードを入力します。

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>Spark のコンピューティング コンテキストを作成する

ワーカー ノード上でデータを読み込んで分析を実行するには、スクリプトのコンピューティング コンテキストを [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark) に設定します。 このコンテキストでは、R 関数はすべてのワーカー ノード全体でワークロードを自動的に分散します。ジョブまたはキューを管理するための組み込み要件はありません。 Spark コンピューティング コンテキストは、`RxSpark` または `rxSparkConnect()` を通じて確立され、`rxSparkDisconnect()` を使用してローカル コンピューティング コンテキストに戻ります。 RStudio に次のコードを入力します。

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>線形モデルを適合させる

1. [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) 関数を使用して、`airDS` データ ソースを使用する線形モデルを適合させます。 RStudio に次のコードを入力します。

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    この手順は、2、3 分で完了します。

1. 結果を表示します。 RStudio に次のコードを入力します。

    ```R
    summary(delayArr)
    ```

    次のような結果が表示されます。

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    ご覧のように、この結果は、指定したディレクトリ内のすべての .csv ファイルを使用して、600 万件すべての観察データが処理されたことを示しています。 また、`cube = TRUE` を指定したため、曜日ごとに推定係数 (切片ではありません) があることもわかります。

## <a name="use-composite-xdf-files"></a>複合 XDF ファイルを使用する

これまで見てきたように、Hadoop 上で R を使用して CSV ファイルを直接分析することができますが、データがより効率的な形式で格納されていると、より迅速に分析できます。 R .xdf 形式はきわめて効率的ですが、個々のファイルが単一の HDFS ブロック内に保持されるように、HDFS 用に多少修正されています (HDFS ブロック サイズはインストールごとに異なりますが、通常は 64 MB か 128 MB です)。Hadoop で [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) を使用する場合は、inData として `AirDS` のような `RxTextData` データ ソースを指定し、outFile 引数として fileSystem が HDFS ファイル システムに設定された `RxXdfData` データ ソースを指定して、複合 .xdf ファイルのセットを作成します。 その後、`RxXdfData` オブジェクトは、後続の R 分析でデータ引数として使用できます。

1. `RxXdfData` オブジェクトを定義します。 RStudio に次のコードを入力します。

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 250,000 行のブロック サイズを設定し、すべてのデータを読み取るように指定します。 RStudio に次のコードを入力します。

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. `rxImport` を使用してデータをインポートします。 RStudio に次のコードを入力します。

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    この手順は、数分で完了します。

1. より高速の新しいデータ ソースを使用して、同じ線形モデルを再予測します。 RStudio に次のコードを入力します。

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    この手順は、1 分かからずに完了します。

1. 結果を表示します。 結果は CSV ファイルの場合と同じになるはずです。 RStudio に次のコードを入力します。

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF から CSV に変換する

### <a name="in-a-spark-context"></a>Spark コンテキストで

分析の実行中の効率を活かすために CSV を XDF に変換したものの、データを CSV に戻したい場合は、[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) を使用して変換できます。

CSV ファイルのフォルダーを作成するには、まずファイル引数としてディレクトリ名を使用して `RxTextData` オブジェクトを作成します。これは、CSV ファイルの作成先となるフォルダーを表します。 `rxDataStep` を実行すると、このディレクトリが作成されます。 次に、`rxDataStep` の `outFile` 引数で、この `RxTextData` オブジェクトを指し示します。 作成された各 CSV には、ディレクトリ名に基づいて名前が付けられ、その後に番号が付けられます。

ロジスティック回帰と予測を実行した後、`airDataXdf` 複合 XDF から HDFS に CSV のフォルダーを書き出して、新しい CSV ファイルに予測値と残差が含まれるようにしたいとします。 RStudio に次のコードを入力します。

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

この手順は、約 2 分半で完了します。

`rxDataStep` によって入力複合 XDF ファイル内の .xdfd ファイルごとに 1 つの CSV が書き出されたことがわかります。 これは、コンピューティング コンテキストが `RxSpark` に設定されている場合に、複合 XDF から HDFS に CSV を書き込む際の既定の動作です。

### <a name="in-a-local-context"></a>ローカル コンテキストで

一方、分析の実行を終えたらコンピューティング コンテキストを `local` に戻し、CSV ファイルを HDFS に書き出すときにもう少し細かい制御ができる 2 つの引数 (`createFileSet` と `rowsPerOutFile`) を `RxTextData` 内で利用することもできます。 `createFileSet` を `TRUE` に設定すると、CSV ファイルのフォルダーは指定したディレクトリに作成されます。 `createFileSet` を `FALSE` に設定すると、単一の CSV ファイルが書き込まれます。 2 つ目の引数の `rowsPerOutFile` に整数を指定すると、`createFileSet` が `TRUE` である場合に各 CSV ファイルに書き込む行数を指定できます。

RStudio に次のコードを入力します。

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

この手順は、約 10 分で完了します。

`RxSpark` コンピューティング コンテキストを使用している場合、`createFileSet` は既定で `TRUE` になり、`rowsPerOutFile` は効果がありません。 そのため、単一の CSV を作成したり、ファイルごとの行数をカスタマイズしたりする場合は、`local` コンピューティング コンテキストで `rxDataStep` を実行する必要があります (その場合でもデータは HDFS に置くことができます)。

## <a name="final-steps"></a>最終手順

1. データをクリーンアップします。 RStudio に次のコードを入力します。

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. リモートの Spark アプリケーションを停止します。 RStudio に次のコードを入力します。

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. R セッションを終了します。 RStudio に次のコードを入力します。

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure HDInsight の ML Services クラスター上で実行されている Apache Spark で R 関数を使用する方法を説明しました。 詳細については、次の記事を参照してください。

* [HDInsight 上の ML Services 向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [Hadoop 上の Spark 用の R 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
