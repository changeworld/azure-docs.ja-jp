---
title: Azure Machine Learning Workbench でのモデル データ収集機能の使用 | Microsoft Docs
description: この記事では、Azure Machine Learning Workbench でモデル データ収集機能を使用する方法について説明します。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5c1a884ebe6216c4e8099f2ada2182ccff68b63e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450332"
---
# <a name="collect-model-data-by-using-data-collection"></a>データ収集を使用してモデル データを収集する

Azure Machine Learning でモデル データ収集機能を使用すると、モデルの入力と Web サービスからの予測をアーカイブできます。

## <a name="install-the-data-collection-package"></a>データ収集パッケージをインストールする
データ収集ライブラリは、Linux および Windows にネイティブにインストールできます。

### <a name="windows"></a>Windows
Windows では、次のコマンドを使用してデータ コレクター モジュールをインストールします。

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linux では、まず libxml++ ライブラリをインストールします。 次のコマンドを実行します。このコマンドは、sudo で実行する必要があります。

    sudo apt-get install libxml++2.6-2v5

次に、次のコマンドを実行します。

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>環境変数の設定

モデル データの収集は、2 つの環境変数に依存します。 AML_MODEL_DC_STORAGE_ENABLED は、**true** (すべて小文字) に設定する必要があります。AML_MODEL_DC_STORAGE には、データを格納する Azure Storage のアカウントへの接続文字列を設定する必要があります。

これらの環境変数は、Web サービスが Azure のクラスターで実行されるときに、前もって設定されます。 ローカルで実行する場合は、自分で設定する必要があります。 Docker を使用する場合は、docker run コマンドの -e パラメーターを使用して、環境変数を渡します。

## <a name="collect-data"></a>データを収集する

モデル データ収集を使用するには、スコア付けファイルに次の変更を加えます。

1. ファイルの先頭に次のコードを追加します。
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. `init()` 関数に次のコード行を追加します。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. `run(input_df)` 関数に次のコード行を追加します。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    `collect()` 関数を呼び出す前に、変数 `input_df` および `pred` (`model.predict()` からの予測値) が初期化されていることを確認してください。

1. `--collect-model-data true` スイッチを指定して `az ml service create realtime` コマンドを使用し、リアルタイムの Web サービスを作成します。 この手順により、サービスが実行されるときに必ずモデル データが収集されるようになります。

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. データ収集をテストするには、`az ml service run realtime` コマンドを実行します。

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>収集したデータを表示する
収集したデータを BLOB ストレージに表示するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択します。
1. 検索ボックスに「**ストレージ アカウント**」と入力し、Enter キーを選択します。
1. **[ストレージ アカウント]** 検索ブレードで、**[ストレージ アカウント]** リソースを選択します。 目的のストレージ アカウントを特定するには、次の手順に従います。

    a. Azure Machine Learning Workbench に移動して作業中のプロジェクトを選択し、**[ファイル]** メニューからコマンド プロンプトを開きます。
    
    b. 「`az ml env show -v`」と入力し、*storage_account* の値を調べます。 それがストレージ アカウントの名前です。

1. リソース ブレードのメニューで **[コンテナー]** を選択し、**modeldata** という名前のコンテナーを選択します。 ストレージ アカウントにデータが反映され始めるまでには、最初の Web サービス要求の後、最大 10 分程度かかる場合があります。 データが次のコンテナー パスで BLOB に取り込まれます。

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

データは、Microsoft のソフトウェアやオープン ソースのツールを含め、さまざまな方法で Azure BLOB から使用することができます。 次に例をいくつか示します。
- Azure Machine Learning Workbench: データ ソースとして .csv ファイルを追加して、Azure Machine Learning Workbench で .csv ファイルを開く
- Excel: 日々の .csv ファイルをスプレッドシートとして開く
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): BLOB の .csv データからプルしたデータを使ってグラフを作成する
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): .csv データの大部分を含んだデータフレームを作成する
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): .csv データを Hive テーブルに読み込み、BLOB に対して直接 SQL クエリを実行する

