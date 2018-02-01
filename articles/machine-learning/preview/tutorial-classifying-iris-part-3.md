---
title: "Azure Machine Learning サービス (プレビュー) のモデルのデプロイ | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 これは 3 部構成の第 3 部で、デプロイ モデルについて取り上げています。"
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/29/2017
ms.openlocfilehash: 97cd46819a4547ec743270871bcb6b4eef3eb365
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2018
---
# <a name="classify-iris-part-3-deploy-a-model"></a>あやめの分類 (パート 3): モデルをデプロイする
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションであり、プロフェッショナルなデータ サイエンティストを対象としています。 データ サイエンティストは、このソリューションを使用してデータの準備、実験の開発、モデルのデプロイをクラウド規模で行うことができます。

このチュートリアルは、3 部構成のシリーズのパート 3 になります。 チュートリアルのこのパートでは、Azure Machine Learning サービス (プレビュー) を使って次の作業を行います。

> [!div class="checklist"]
> * モデル ファイルを探す
> * スコア付けスクリプトとスキーマ ファイルを生成する。
> * 環境を準備する。
> * リアルタイム Web サービスを作成する。
> * リアルタイム Web サービスを実行する。
> * 出力された BLOB データを確認する。 

 このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、Mac OS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件
このチュートリアル シリーズのパート 1 とパート 2 を完了してください。

   * [データの準備に関するチュートリアル](tutorial-classifying-iris-part-1.md)に従って Machine Learning リソースを作成し、Azure Machine Learning Workbench アプリケーションをインストールします。

   * [モデルの構築に関するチュートリアル](tutorial-classifying-iris-part-2.md)に従って、Azure Machine Learning でロジスティック回帰モデルを作成します。

Docker エンジンをインストールし、ローカルで実行している必要があります。 または、Azure の Azure Container Service クラスターにデプロイすることもできます。

## <a name="download-the-model-pickle-file"></a>モデルの pickle ファイルをダウンロードする
このチュートリアルの前のパートでは、ローカルの Machine Learning Workbench で **iris_sklearn.py** スクリプトを実行しました。 その際、Python オブジェクトのシリアル化パッケージとして広く使われている [pickle](https://docs.python.org/2/library/pickle.html) を使ってロジスティック回帰モデルをシリアル化しました。 

1. Machine Learning Workbench アプリケーションを開き、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトが開いたら、左側のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダーのファイルを一覧表示します。

3. **iris_sklearn.py** ファイルを選択します。 Workbench の新しいテキスト エディター タブに Python コードが表示されます。

4. pickle ファイルが生成されている場所を **iris_sklearn.py** ファイルで確認します。 Ctrl + F キーを押して **[検索]** ダイアログ ボックスを開き、Python コード内で **pickle** という単語を探してください。

   このコード スニペットを見ると、pickle の出力ファイルがどのように生成されているのかがわかります。 pickle の出力ファイルは、**model.pkl** という名前でディスクに保存されます。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 前回実行時の出力ファイルからモデルの pickle ファイルを探します。
   
   **iris_sklearn.py** スクリプトを実行したときに、モデル ファイルは **model.pkl** という名前で **outputs** フォルダーに書き込まれています。 このフォルダーは、ローカルのプロジェクト フォルダーにではなく、スクリプトの実行場所として選んだ実行環境に存在します。 
   
   - ファイルを探すには、左側のウィンドウにある **[実行]** ボタン (時計のアイコン) を選択して **[すべての実行]** の一覧を開きます。  
   - **[すべての実行]** タブが表示されます。 実行のテーブルで、最近の実行をどれか 1 つ選択します。対象の環境が **[local]** で、スクリプト名が **[iris_sklearn.py]** であるものを選んでください。 
   - **[Run Properties]\(実行プロパティ\)** ウィンドウが開きます。 ウィンドウの右上のセクションにある **[出力]** セクションに注目してください。 
   - pickle ファイルをダウンロードするには、**model.pkl** ファイルの横のチェック ボックスをオンにし、**[ダウンロード]** ボタンを選択します。 これをプロジェクト フォルダーのルートに保存してください。 このファイルは、この後の手順で必要になります。

   ![pickle ファイルをダウンロードする](media/tutorial-classifying-iris/download_model.png)

   `outputs` フォルダーについて詳しくは、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関する記事を参照してください。

## <a name="get-the-scoring-script-and-schema-files"></a>スコア付けスクリプトとスキーマ ファイルを取得する
Web サービスをモデル ファイルと一緒にデプロイするには、スコア付けスクリプトのほか、Web サービスの入力データに使用するスキーマ (任意) も必要になります。 スコア付けスクリプトを実行すると、現在のフォルダーから **model.pkl** ファイルが読み込まれ、このファイルを使用して、新しく予測されたあやめの分類が生成されます。  

1. Azure Machine Learning Workbench アプリケーションを開き、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトが開いたら、左側のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダーのファイルを一覧表示します。

3. **score_iris.py** ファイルを選択します。 Python スクリプトが表示されます。 このファイルは、スコア付けファイルとして使用されます。

   ![スコア付けファイル](media/tutorial-classifying-iris/model_data_collection.png)

4. スキーマ ファイルを取得するには、このスクリプトを実行します。 コマンド バーで **[local]** 環境と **[score_iris.py]** スクリプトを選択し、**[実行]** ボタンを選択します。 

5. このスクリプトを実行すると、**[出力]** セクションに JSON ファイルが作成されます。このファイルによって、モデルに必要な入力データ スキーマがキャプチャされます。

6. **[プロジェクト ダッシュボード]** ウィンドウの右側の **[ジョブ]** ウィンドウに注目します。 最新の **score_iris.py** ジョブが緑色の **[完了]** 状態になるまで待ちます。 次に、最新のジョブ実行の **score_iris.py [1]** というハイパーリンクを選択すると、**score_iris.py** 実行からの実行の詳細が表示されます。 

7. **[Run Properties]\(実行プロパティ\)** ウィンドウの **[出力]** セクションで、新しく作成された **service_schema.json** ファイルを選択します。  ファイル名の横のチェック ボックスをオンにし、**[ダウンロード]** を選択します。 このファイルをプロジェクトのルート フォルダーに保存してください。

8. 先ほど **score_iris.py** スクリプトを開いたタブに戻ります。 データ収集を使用して、Web サービスからモデルの入力と予測をキャプチャすることができます。 以降の手順は、データ収集で特に重要となります。

9. モデル データの収集機能は **ModelDataCollector** クラスに含まれています。そこで、このクラスがファイルの冒頭のコードによってインポートされることを確認します。

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. **init()** 関数の次のコード行によって、**ModelDataCollector** がインスタンス化されることを確認します。

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. **run(input_df)** 関数の次のコード行によって、入力データと予測データが収集されることを確認します。

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

これで、モデルを運用するための環境を準備する用意が整いました。



## <a name="prepare-to-operationalize-locally"></a>ローカルで運用できるように準備する
ローカル コンピューター上の Docker コンテナーで実行するには、"_ローカル モード_" デプロイを使います。

"_ローカル モード_" は、開発とテストに使うことができます。 以降の手順でモデルを運用可能な状態にするためには、Docker エンジンがローカルで実行されている必要があります。 コマンドの最後に `-h` フラグを付けると、コマンドのヘルプを参照できます。

>[!NOTE]
>Docker エンジンがローカルで実行されていない場合、デプロイ用のクラスターを Azure に作成することで手順を続行できます。 それ以上料金が発生しないよう、クラスターはチュートリアル後に必ず削除してください。

1. コマンド ライン インターフェイス (CLI) を開きます。
   Azure Machine Learning Workbench アプリケーションの **[ファイル]** メニューの **[コマンド プロンプトを開く]** を選択してください。

   現在のプロジェクト フォルダーの場所 (**c:\temp\myIris>**) でコマンド ライン プロンプトが開きます。

2. 環境を作成します。 この手順は、環境ごとに 1 回実行する必要があります。 たとえば開発環境で 1 回、運用環境で 1 回実行します。 この最初の環境には "_ローカル モード_" を使います  後から "_クラスター モード_" で環境を設定してみる場合は、次のコマンドで `-c` スイッチまたは `--cluster` スイッチを指定してください。

   次の setup コマンドを実行するには、サブスクリプションの共同作成者のアクセス権が必要です。 このアクセス権がない場合、最低でも、デプロイ先のリソース グループに対する共同作成者のアクセス権が必要となります。 後者の場合、setup コマンドで `-g` フラグを使用し、リソース グループの名前を指定する必要があります。 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   画面の指示に従い、Docker イメージを格納するためのストレージ アカウント、Docker イメージをリストする Azure Container Registry、テレメトリを収集する AppInsight アカウントをプロビジョニングします。 `-c` スイッチを使用した場合、Azure Container Service クラスターも作成されます。
   
   クラスター名によって環境を識別できます。 場所には、Azure Portal で作成したモデル管理アカウントと同じ場所を指定する必要があります。

3. モデル管理アカウントを作成します。 (これは 1 回限りの作業です)。  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. モデル管理アカウントを設定します。  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. 環境を設定します。

   設定が完了したら、環境の運用化に必要な環境変数を次のコマンドで設定します。 環境名には、前の手順 2. で使用した名前を使用します。 リソース グループ名には、セットアップ プロセスが完了したときにコマンド ウィンドウに出力された名前を使用します。

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. ローカル Web サービスのデプロイ用に運用可能にした環境が適切に構成されていることを確認するには、次のコマンドを入力します。

   ```azurecli
   az ml env show
   ```

これで、リアルタイム Web サービスを作成する準備が整いました。

>[!NOTE]
>モデル管理アカウントと環境は、その後の Web サービスのデプロイに再利用することができます。 これらを Web サービスごとに作成する必要はありません。 1 つのアカウントまたは環境に複数の Web サービスを関連付けることができます。

## <a name="create-a-real-time-web-service-in-one-command"></a>1 つのコマンドでリアルタイム Web サービスを作成する
1. リアルタイム Web サービスを作成するには、次のコマンドを使用します。

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   このコマンドにより、後で使用できる Web サービス ID が生成されます。

   **az ml service create realtime** コマンドでは、次のスイッチを使用します。
   * `-n`: アプリ名。すべて小文字にする必要があります。
   * `-f`: スコア付けスクリプト ファイルの名前。
   * `--model-file`: モデル ファイル。 このケースでは、pickle から出力された model.pkl ファイルになります。
   * `-r`: モデルの種類。 このケースでは、Python モデルになります。
   * `--collect-model-data true`: データ収集を有効にします。
   * `-c`: 追加のパッケージが指定されている conda 依存関係ファイルのパス。

   >[!IMPORTANT]
   >サービス名 (新しい Docker イメージ名) はすべて小文字であることが必要です。 そうしないと、エラーが発生します。 

2. このコマンドを実行すると、環境のセットアップの一環として作成したストレージ アカウントに、モデル ファイルとスコア付けファイルがアップロードされます。 このデプロイ プロセスによって、モデル、スキーマ、スコア付けファイルが含まれた Docker イメージが作成され、Azure Container Registry (**\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**) にプッシュされます。 

   さらに、そのイメージがローカルのコンピューターにプルダウンされ、そのイメージに基づいて Docker コンテナーが起動されます。 ご利用の環境がクラスター モードで構成されている場合は、Azure Cloud Services の Kubernetes クラスターに Docker コンテナーがデプロイされます。

   デプロイの一環として、Web サービスの HTTP REST エンドポイントがローカル マシンに作成されます。 数分後、正常終了のメッセージと共にコマンドが完了し、Web サービスの準備が整います。

3. 実行中の Docker コンテナーを確認するには、**docker ps** コマンドを使用します。
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>個別のコマンドを使用してリアルタイム Web サービスを作成する
前述の **az ml service create realtime** コマンドの代わりに、手順を個別に実行することもできます。 

まずモデルを登録します。 次にマニフェストを生成し、Docker イメージを作成して、Web サービスを作成します。 この段階的なアプローチにより、各手順での柔軟性が高まります。 また、前の手順で生成されたエンティティを再利用したり、必要な場合にのみエンティティを再構築したりできます。

1. pickle ファイル名を指定してモデルを登録します。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   このコマンドにより、モデル ID が生成されます。

2. マニフェストを作成します。

   マニフェストを作成するには、前の手順で出力されたモデル ID を指定して次のコマンドを実行します。

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   このコマンドにより、マニフェスト ID が生成されます。

3. Docker イメージを作成します。

   Docker イメージを作成するには、前の手順で出力されたマニフェスト ID の値を指定して次のコマンドを実行します。 必要に応じて、`-c` スイッチを使用して conda 依存関係を含めることもできます。

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c amlconfig\conda_dependencies.yml
   ```
   このコマンドにより、Docker イメージ ID が生成されます。
   
4. サービスを作成します。

   サービスを作成するには、前の手順で出力されたイメージ ID を指定して次のコマンドを実行します。

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   このコマンドにより、Web サービス ID が生成されます。

これで、Web サービスを実行する準備が整いました。

## <a name="run-the-real-time-web-service"></a>リアルタイム Web サービスを実行する

実行中の **irisapp** Web サービスをテストするために、4 つの乱数の配列を含む JSON エンコードされたレコードを使用します。

1. この Web サービスにはサンプル データが含まれています。 ローカル モードで実行している場合は、**az ml service usage realtime** コマンドを呼び出すことができます。 この呼び出しにより、サービスのテストに役立つサンプル実行コマンドが取得されます。 また、独自のカスタム アプリにサービスを組み込む際に使用できるスコア付け URL も取得されます。

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. サービスをテストするには、返された service run コマンドを実行します。

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   出力は **"2"** です。これは予測された品種を表します  (実際の結果は異なる場合があります)。 

3. CLI 以外からサービスを実行するには、認証用のキーを取得する必要があります。

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Azure Blob Storage に収集されたデータを確認する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 自分のストレージ アカウントを探します。 これを行うには、**[その他のサービス]** を選択します。

3. 検索ボックスに「**ストレージ アカウント**」と入力し、**Enter** キーを押します。

4. **[ストレージ アカウント]** 検索ボックスで、自分の環境に該当する **[ストレージ アカウント]** リソースを選択します。 

   > [!TIP]
   > 使用中のストレージ アカウントを調べるには、次の手順を実行します。
   > 1. Azure Machine Learning Workbench を開きます。
   > 2. 作業中のプロジェクトを選択します。
   > 3. **[ファイル]** メニューからコマンド ライン プロンプトを開きます。
   > 4. コマンド ライン プロンプトに「`az ml env show -v`」と入力し、*storage_account* の値を確認してください。 それがストレージ アカウントの名前です。

5. **[ストレージ アカウント]** ウィンドウが開いたら、左側の一覧から **[コンテナー]** を選択します。 **modeldata** という名前のコンテナーを探します。 
 
   データがまったく表示されない場合は少しお待ちください。最初の Web サービス要求の後、ストレージ アカウントにデータが反映されるまでに、最大 10 分程度かかる場合があります。

   データが次のコンテナー パスで BLOB に取り込まれます。

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. このデータを Azure Blob Storage から取り出して利用することができます。 データは、次のような Microsoft のソフトウェアやオープン ソースの各種ツールで使用できます。

   - Azure Machine Learning: データ ソースとして CSV ファイルを追加して開く。 
   - Excel: 日々の CSV ファイルをスプレッドシートとして開く。
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): BLOB の CSV データからプルしたデータを使ってグラフを作成する。
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): CSV データを Hive テーブルに読み込み、BLOB に対して直接 SQL クエリを実行する。
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): CSV データの大部分を含んだデータフレームを作成する。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>次の手順
3 部構成のチュートリアル シリーズのパート 3 では、Azure Machine Learning サービスを使って次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * モデル ファイルを探す
> * スコア付けスクリプトとスキーマ ファイルを生成する。
> * 環境を準備する。
> * リアルタイム Web サービスを作成する。
> * リアルタイム Web サービスを実行する。
> * 出力された BLOB データを確認する。 

さまざまなコンピューティング環境でトレーニング スクリプトの実行、モデルの作成、モデルのシリアル化を行い、Docker ベースの Web サービスでモデルが運用できる状態となりました。 

高度なデータ準備をいつでも行うことができます。
> [!div class="nextstepaction"]
> [高度なデータ準備](tutorial-bikeshare-dataprep.md)
