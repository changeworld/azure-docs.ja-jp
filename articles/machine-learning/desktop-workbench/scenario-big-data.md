---
title: 数テラバイトのデータを活用したサーバー ワークロードの予測 - Azure | Microsoft Docs
description: Azure Machine Learning Workbench を使用して、ビッグ データで機械学習モデルをトレーニングする方法について説明します。
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: 7a13cafd3dcfb4637a5deae2c678c518019ad168
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450672"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>数テラバイトのデータを活用したサーバー ワークロードの予測

この記事では、データ サイエンティストが Azure Machine Learning Workbench を使用して、ビッグ データを使用する必要があるソリューションを開発する方法について説明します。 大規模なデータセットのサンプルから始め、データ準備、特徴エンジニアリング、機械学習を繰り返した後、大規模なデータセット全体にプロセスを拡張できます。 

Machine Learning Workbench の次の主な機能について説明します。
* コンピューティング ターゲット間の簡単な切り替え:  さまざまなコンピューティング ターゲットを設定し、実験で使用できます。 この例では、Ubuntu DSVM と Azure HDInsight クラスターをコンピューティング ターゲットとして使用します。 また、リソースの可用性に応じて、コンピューティング ターゲットを構成することもできます。 具体的には、ワーカー ノードを追加して Spark クラスターをスケールアウトした後、Machine Learning Workbench でリソースを使用して実験の実行を高速化できます。
* 実行履歴の追跡:  Machine Learning Workbench を使用して、機械学習モデルのパフォーマンスや関心がある他のメトリックを追跡できます。
* 機械学習モデルの運用化:  Machine Learning Workbench 内で組み込みのツールを使用して、トレーニングされた機械学習モデルを Azure Container Service の Web サービスとしてデプロイできます。 また、その Web サービスを使用して、REST API の呼び出しで少量のバッチ予測を取得することもできます。 
* テラバイト単位のサポート。

> [!NOTE]
> コード サンプルとこの例に関連するその他の資料については、[GitHub](https://github.com/Azure/MachineLearningSamples-BigData) を参照してください。
> 

## <a name="use-case-overview"></a>ユース ケースの概要

サーバー上のワークロードの予測は、自社のインフラストラクチャを管理するテクノロジ企業に共通するビジネス ニーズです。 インフラストラクチャ コストを削減するには、あまり使用されていないサーバーで実行されているサービスをまとめて少数のコンピューターで実行します。 過度に使用されているサーバーで実行されているサービスには、より多くのコンピューターを割り当てます。 

このシナリオでは、各コンピューター (またはサーバー) のワークロード予測に重点を置きます。 具体的には、各サーバー上のセッション データを使用して、今後のサーバーのワークロード クラスを予測します。 [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html) の Random Forest Classifier を使用して、各サーバーの負荷を低、中、高の各クラスに分類します。 この例の機械学習手法とワークフローは、他の同様の問題に簡単に拡張できます。 


## <a name="prerequisites"></a>前提条件

この例を実行するための前提条件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版も使用できます)。
* [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) のインストール済みコピー。 プログラムをインストールし、ワークスペースを作成するには、[クイックスタート インストール ガイド](../service/quickstart-installation.md)を参照してください。 複数のサブスクリプションをお持ちの場合は、[使うサブスクリプションを現在アクティブなサブスクリプションに設定する](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set)ことができます。
* Windows 10 (この例の手順は、macOS システムでもほぼ同じです)。
* Linux (Ubuntu) 用のデータ サイエンス仮想マシン (DSVM)。できれば、データが存在する米国東部リージョンにします。 [こちらの手順](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)に従って、Ubuntu DSVM をプロビジョニングできます。 [こちらのクイックスタート](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)も参照してください。 少なくとも 8 個のコアと 32 GB のメモリを搭載した仮想マシンを使用することをお勧めします。 

[こちらの手順](../service/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present)に従って、AML Workbench の VM でパスワードのない sudoer アクセスを有効にします。  [AML Workbench の VM の作成と使用に SSH キーに基づく認証](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets)を使うこともできますです。 この例では、パスワードを使って VM にアクセスします。  後の手順で参照できるように、次の表に DSVM の情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
DSVM の IP アドレス | xxx|
 ユーザー名  | xxx|
 パスワード   | xxx|


 [Docker エンジン](https://docs.docker.com/engine/)がインストールされている VM を使用できます。

* Hortonworks Data Platform バージョン 3.6 および Spark バージョン 2.1.x がインストールされた HDInsight Spark クラスター。できれば、データが存在する米国東部リージョンのもの。 HDInsight クラスターの作成方法の詳細については、「[Azure HDInsight での Apache Spark クラスターの作成](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)」をご覧ください。 各 worker に 16 コアと 112 GB のメモリを割り当てた 3 worker クラスターを使用することをお勧めします。 または、VM の種類としてヘッド ノードに `D12 V2`、ワーカー ノードに `D14 V2` を選択することもできます。 クラスターのデプロイには約 20 分かかります。 この例を試すには、クラスター名、SSH ユーザー名、およびパスワードが必要です。 後の手順で参照できるように、次の表に Azure HDInsight クラスターの情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
 クラスター名| xxx|
 ユーザー名  | xxx (既定では sshuser)|
 パスワード   | xxx|


* Azure Storage のアカウント [こちらの手順](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)に従って、Azure ストレージ アカウントを作成できます。 また、このストレージ アカウントに、`fullmodel` および `onemonthmodel` という名前の 2 つのプライベート BLOB コンテナーを作成します。 ストレージ アカウントは、中間の計算結果と機械学習モデルを保存するために使用されます。 この例を試すには、ストレージ アカウント名とアクセス キーが必要です。 後の手順で参照できるように、次の表に Azure ストレージ アカウントの情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
 ストレージ アカウント名| xxx|
 アクセス キー  | xxx|


前提条件一覧で作成した Ubuntu DSVM と Azure HDInsight クラスターは、コンピューティング ターゲットです。 コンピューティング ターゲットは、Machine Learning Workbench のコンテキストのコンピューティング リソースです。このリソースは、Workbench が実行されているコンピューターとは異なる場合があります。   

## <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Machine Learning Workbench を開きます。
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します。
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに、「**Workload Forecasting on Terabytes Data**」と入力し、テンプレートを選択します。
5.  **作成**を選択します。

[こちらの手順](./tutorial-classifying-iris-part-1.md)に従って事前に作成した Git リポジトリを使用して、Workbench プロジェクトを作成できます。  
バージョンを追跡するために、`git status` を実行してファイルの状態を検査します。

## <a name="data-description"></a>データの説明

この例で使用するデータは、合成されたサーバー ワークロード データです。 米国東部リージョンでパブリックにアクセス可能な Azure Blob ストレージ アカウント内でホストされています。 具体的なストレージ アカウント情報は、[`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) の `dataFile` フィールドに "wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>" の形式で記述されています。 Blob Storage から直接データを使用できます。 多数のユーザーが同時にストレージを使用する場合は、よりよい実験エクスペリエンスのため、[azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) を使ってデータを自分のストレージにダウンロードできます。 

合計データ サイズは約 1 TB です。 各ファイルは約 1 ～ 3 GB であり、ヘッダーなしの CSV ファイル形式です。 データの各行は、特定のサーバーで発生したトランザクションの負荷を示しています。 データ スキーマの詳細情報は次のとおりです。

列番号 | フィールド名| type | 説明 |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    セッションの開始時間
2  |`SessionEnd`    | DateTime | セッションの終了時間
3 |`ConcurrentConnectionCounts` | 整数 | 同時接続数
4 | `MbytesTransferred` | Double | メガバイト単位で転送される正規化されたデータ
5 | `ServiceGrade` | 整数 |  セッションのサービス グレード
6 | `HTTP1` | 整数|  セッションで HTTP1 と HTTP2 のどちらを使用するか
7 |`ServerType` | 整数   |サーバーの種類
8 |`SubService_1_Load` | Double |   サブサービス 1 の負荷
9 | `SubService_2_Load` | Double |  サブサービス 2 の負荷
10 | `SubService_3_Load` | Double |     サブサービス 3 の負荷
11 |`SubService_4_Load` | Double |  サブサービス 4 の負荷
12 | `SubService_5_Load`| Double |      サブサービス 5 の負荷
13 |`SecureBytes_Load`  | Double | セキュリティで保護されたバイトの読み込み
14 |`TotalLoad` | Double | サーバー上の合計読み込み
15 |`ClientIP` | String|    クライアント IP アドレス
16 |`ServerIP` | String|    サーバーの IP アドレス



上記の表には予想されるデータ型が記載されています。 欠落値やダーティ データの問題により、データ型が実際に予想どおりであるという保証はありません。 データ処理ではこの点を考慮してください。 


## <a name="scenario-structure"></a>シナリオの構造

この例のファイルは、次のように整理されます。

| ファイル名 | type | 説明 |
|-----------|------|-------------|
| `Code` | フォルダー | このフォルダーには、この例のすべてのコードが含まれています。 |
| `Config` | フォルダー | このフォルダーには、構成ファイルが含まれています。 |
| `Image` | フォルダー | README ファイルの画像を保存するためのフォルダー。 |
| `Model` | フォルダー | Blob Storage からダウンロードしたモデル ファイルを保存するためのフォルダー。 |
| `Code/etl.py` | Python ファイル | データ準備と特徴エンジニアリングに使用される Python ファイル。 |
| `Code/train.py` | Python ファイル | 3 クラスの多分類モデルのトレーニングに使用される Python ファイル。  |
| `Code/webservice.py` | Python ファイル | 運用化に使用される Python ファイル。  |
| `Code/scoring_webservice.py` | Python ファイル |  データ変換と Web サービスの呼び出しに使用される Python ファイル。 |
| `Code/O16Npreprocessing.py` | Python ファイル | scoring_webservice.py のデータの前処理に使用される Python ファイル。  |
| `Code/util.py` | Python ファイル | Azure BLOB の読み取りと書き込みのコードを含む Python ファイル。  
| `Config/storageconfig.json` | JSON ファイル | 1 か月のデータの処理とトレーニングを行うために、中間結果とモデルを格納する Azure BLOB コンテナーの構成ファイル。 |
| `Config/fulldata_storageconfig.json` | JSON ファイル | 完全なデータセットの処理とトレーニングを行うために、中間結果とモデルを格納する Azure BLOB コンテナーの構成ファイル。|
| `Config/webservice.json` | JSON ファイル | scoring_webservice.py の構成ファイル。|
| `Config/conda_dependencies.yml` | YAML ファイル | Conda の依存関係ファイル。 |
| `Config/conda_dependencies_webservice.yml` | YAML ファイル | Web サービス用の Conda の依存関係ファイル。|
| `Config/dsvm_spark_dependencies.yml` | YAML ファイル | Ubuntu DSVM 用の Spark の依存関係ファイル。 |
| `Config/hdi_spark_dependencies.yml` | YAML ファイル | HDInsight Spark クラスター用の Spark の依存関係ファイル。 |
| `README.md` | マークダウン ファイル | README マークダウン ファイル。 |
| `Code/download_model.py` | Python ファイル | Azure BLOB のモデル ファイルをローカル ディスクにダウンロードするために使用される Python ファイル。 |
| `Docs/DownloadModelsFromBlob.md` | マークダウン ファイル | `Code/download_model.py` の実行手順を含むマークダウン ファイル。 |



### <a name="data-flow"></a>Data flow

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) のコードは、パブリックでアクセス可能なコンテナー ([`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) の `dataFile` フィールド) からデータを読み込みます。 データの準備と特徴エンジニアリングが含まれ、中間の計算結果とモデルが自分のプライベート コンテナーに保存されます。 [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) のコードでは、プライベート コンテナーから中間計算結果を読み込み、多クラス分類モデルをトレーニングし、トレーニングされた機械学習モデルをプライベート コンテナーに書き込みます。 

1 か月のデータセットでの実験用に 1 つのコンテナーを使用し、完全なデータセットでの実験用に別のコンテナーを使用することをお勧めします。 データとモデルは Parquet ファイルとして保存されるので、各ファイルは実際には複数の BLOB を含むコンテナー内の 1 つのフォルダーです。 結果のコンテナーは次のようになります。

| BLOB のプレフィックス名 | type | 説明 |
|-----------|------|-------------|
| featureScaleModel | Parquet | 数値特徴の標準スケーラー モデル。 |
| stringIndexModel | Parquet | 数値特徴以外の文字列インデクサー モデル。|
| oneHotEncoderModel|Parquet | カテゴリの特徴のワンホット エンコーダー モデル。 |
| mlModel | Parquet | トレーニングされた機械学習モデル。 |
| info| Python pickle ファイル | 変換されたデータに関する情報。たとえば、トレーニングの開始、トレーニングの終了、期間、トレーニングとテストの分割のタイムスタンプ、インデックス作成とワンホット エンコーディングの列などです。

上記の表のファイルと BLOB はすべて運用化に使用されます。


### <a name="model-development"></a>モデルの開発

#### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム


次の図は、Machine Learning Workbench を使用してモデルを開発するエンド ツー エンドのワークフローを示しています。![アーキテクチャ](media/scenario-big-data/architecture.PNG)

以下のセクションでは、Machine Learning Workbench でリモート コンピューティング ターゲット機能を使用したモデル開発について説明します。 短時間で繰り返し実行できるように、まず Ubuntu DSVM に少量のサンプル データを読み込み、[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) スクリプトを実行します。 さらに短時間で繰り返し実行できるように追加の引数を渡して、[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) で実行する作業を制限することができます。 最後に、HDInsight クラスターを使用して完全なデータでトレーニングします。     

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) ファイルでは、データを読み込んで準備し、特徴エンジニアリングを実行します。 このファイルは、次の 2 つの引数を受け取ります。
* 中間計算結果とモデルを格納するための Blob Storage コンテナーの構成ファイル
* 短時間で繰り返し実行するためのデバッグ構成引数

最初の引数である `configFilename` は、Blob Storage 情報を格納し、データを読み込む場所を指定するローカル構成ファイルです。 既定では、[`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) であり、1 か月のデータ実行で使用されます。 また、[`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) も含まれています。これは、完全なデータセット実行で使用する必要があります。 構成の内容は次のとおりです。 

| フィールド | type | 説明 |
|-----------|------|-------------|
| storageAccount | String | Azure ストレージ アカウント名 |
| storageContainer | String | 中間の結果を格納する Azure ストレージ アカウントのコンテナー |
| storageKey | String |Azure ストレージ アカウントのアクセス キー |
| dataFile|String | データ ソース ファイル  |
| duration| String | データ ソース ファイルのデータの期間|

`Config/storageconfig.json` と `Config/fulldata_storageconfig.json` の両方を変更して、ストレージ アカウント、ストレージ キー、および BLOB コンテナーを構成して中間の結果を格納します。 既定では、1 か月のデータ実行の BLOB コンテナーは `onemonthmodel` で、完全なデータセット実行の BLOB コンテナーは `fullmodel` です。 ストレージ アカウントにこれら 2 つのコンテナーを作成するようにします。 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) の `dataFile` フィールドで、[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) に読み込まれるデータを構成します。 `duration` フィールドで、データに含める範囲を構成します。 duration を ONE_MONTH に設定すると、読み込まれるデータは、2016 年 6 月のデータの 7 つのファイルのうち 1 つの .csv ファイルだけになります。 duration が FULL の場合、完全なデータセット (1 TB) が読み込まれます。 これら 2 つの構成ファイルに含まれる `dataFile` と `duration` を変更する必要はありません。

2 つ目の引数は DEBUG です。 FILTER_IP に設定すると、短時間で繰り返すことができます。 このパラメーターは、スクリプトをデバッグするときに使用すると便利です。

> [!NOTE]
> 以降のすべてのコマンドで、引数の変数を実際の値に置き換えてください。
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Ubuntu DSVM 上の Docker でのモデル開発

#####  <a name="1-set-up-the-compute-target"></a>1.コンピューティング ターゲットの設定

**[ファイル]** > **[コマンド プロンプトを開く]** を選択して、Machine Learning Workbench からコマンド ラインを起動します。 次に、以下を実行します。 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

プロジェクトの aml_config フォルダーに、次の 2 つのファイルが作成されます。

-  dockerdsvm.compute: このファイルには、リモートの実行ターゲットの接続および構成情報が含まれています。
-  dockerdsvm.runconfig: このファイルは、Workbench アプリケーション内で使用される一連の実行オプションです。

dockerdsvm.runconfig を参照し、フィールドの構成を次のように変更します。

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

以下を実行してプロジェクト環境を準備します。

```az ml experiment prepare -c dockerdsvm```


`PrepareEnvironment` を true に設定すると、ジョブを送信するたびに、Machine Learning Workbench によってランタイム環境が作成されます。 `Config/conda_dependencies.yml` と `Config/dsvm_spark_dependencies.yml` には、ランタイム環境のカスタマイズが含まれています。 これら 2 つの YMAL ファイルを編集して、いつでも Conda の依存関係、Spark の構成、および Spark の依存関係を変更できます。 この例では、追加の Python パッケージとして `azure-storage` と `azure-ml-api-sdk` を `Config/conda_dependencies.yml` に追加しました。 また、`spark.default.parallelism`、`spark.executor.instances`、`spark.executor.cores` を `Config/dsvm_spark_dependencies.yml` に追加しました。 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2.DSVM Docker 上のデータの準備と特徴エンジニアリング

DSVM Docker 上で `etl.py` スクリプトを実行します。 特定のサーバー IP アドレスで読み込まれるデータをフィルター処理するデバッグ パラメーターを使用します。

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

サイド パネルに移動し、**[実行]** をクリックすると、`etl.py` の実行履歴が表示されます。 実行時間は約 2 分です。 コードを変更して新機能を含めることを予定している場合は、2 つ目の引数として FILTER_IP を指定することで、短時間で繰り返し実行できるようになります。 データセットを探索したり、新機能を作成したりするために、独自の機械学習の問題に対処するときに、この手順を何回も実行することが必要な場合があります。 

読み込むデータの制限をカスタマイズし、処理するデータをさらにフィルター処理することで、モデル開発での繰り返し処理を高速化することができます。 実験するときは、コードの変更を Git リポジトリに定期的に保存することをお勧めします。 `etl.py` では次のコードを使用し、プライベート コンテナーへのアクセスを有効にしました。

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


次に、デバッグ パラメーター FILTER_IP を使用せずに、DSVM Docker で `etl.py` スクリプトを実行します。

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

サイド パネルに移動し、**[実行]** をクリックすると、`etl.py` の実行履歴が表示されます。 実行時間は約 4 分です。 この手順の処理結果はコンテナーに保存され、トレーニングのために train.py に読み込まれます。 また、文字列インデクサー、エンコーダー パイプライン、標準スケーラーもプライベート コンテナーに保存されます。 これらは運用化で使用されます。 


##### <a name="3-model-training-on-dsvm-docker"></a>手順 3.DSVM Docker でのモデルのトレーニング

DSVM Docker 上でスクリプト `train.py` を実行します。

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

この手順では、`etl.py` の実行から中間計算結果を読み込み、機械学習モデルをトレーニングします。 この手順には約 2 分かかります。

少量のデータで実験を正常に完了したら、完全なデータセットで引き続き実験を実行できます。 まず、同じコードを使用して実行してから、引数とコンピューティング ターゲットを変更して実験を実行できます。  

####  <a name="model-development-on-the-hdinsight-cluster"></a>HDInsight クラスター上でのモデル開発

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1.HDInsight クラスター用に Machine Learning Workbench にコンピューティング ターゲットを作成する

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

aml_config フォルダーに、次の 2 つのファイルが作成されます。
    
-  myhdi.compute: このファイルには、リモートの実行ターゲットの接続および構成情報が含まれています。
-  myhdi.runconfig: このファイルは、Workbench アプリケーション内で使用される一連の実行オプションです。


myhdi.runconfig を参照し、フィールドの構成を次のように変更します。

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

以下を実行してプロジェクト環境を準備します。

```az ml experiment prepare -c myhdi```

この手順の実行時間は最長 7 分です。

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2.HDInsight クラスター上のデータの準備と特徴エンジニアリング

HDInsight クラスター上で完全なデータを使用して `etl.py` スクリプトを実行します。

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

このジョブの実行時間は比較的長いので (約 2 時間)、`-a` を使用して出力ストリーミングを無効にすることができます。 ジョブが完了したら、**[実行履歴]** でドライバー ログとコントローラー ログを確認できます。 大規模なクラスターがある場合は、より多くのインスタンスまたはコアを使用するように、`Config/hdi_spark_dependencies.yml` で構成をいつでも変更できます。 たとえば、4 ワーカー ノードのクラスターがある場合は、`spark.executor.instances` の値を 5 から 7 に増やすことができます。 ストレージ アカウントの **fullmodel** コンテナーでこの手順の出力を確認できます。 


##### <a name="3-model-training-on-hdinsight-cluster"></a>手順 3.HDInsight クラスター上のモデルのトレーニング

HDInsight クラスター上でスクリプト `train.py` を実行します。

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

このジョブの実行時間は比較的長いので (約 30 分)、`-a` を使用して出力ストリーミングを無効にすることができます。

#### <a name="run-history-exploration"></a>実行履歴の探索

実行履歴は、Machine Learning Workbench での実験を追跡できる機能です。 既定では、実験の期間が追跡されます。 この例では、実験で `Code/etl.py` の完全なデータセットに移行すると、期間が大幅に増えることがわかります。 追跡のために、特定のメトリックをログに記録することもできます。 メトリックの追跡を有効にするには、Python ファイルのヘッダーに次のコード行を追加します。
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
以下は特定のメトリックを追跡する例です。

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Workbench の右側のサイドバーで **[実行]** に移動すると、各 Python ファイルの実行履歴が表示されます。 GitHub リポジトリに移動することもできます。 各実行でスクリプトに加えられた変更を追跡するために、"AMLHistory" で始まる名前の新しいブランチが作成されます。 


### <a name="operationalize-the-model"></a>モデルの運用化

このセクションでは、前の手順で Web サービスとして作成したモデルを運用化します。 また、Web サービスを使用してワークロードを予測する方法についても説明します。 Machine Language Operationalization コマンド ライン インターフェイス (CLI) を使用して、コードと依存関係を Docker イメージとしてパッケージ化し、コンテナー化された Web サービスとしてモデルを公開します。

Machine Learning Workbench のコマンド ライン プロンプトを使用して CLI を実行できます。  また、[インストール ガイド](./deployment-setup-configuration.md#using-the-cli)に従って、Ubuntu Linux 上で CLI を実行することもできます。 

> [!NOTE]
> 以降のすべてのコマンドで、引数の変数を実際の値に置き換えてください。 このセクションを完了するには、約 40 分かかります。
> 

運用化のための環境として一意の文字列を選択します。 ここでは、選択した文字列を表すために "[unique]" という文字列を使用します。

1. 運用化のための環境を作成し、リソース グループを作成します。

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   `az ml env setup` コマンドで `--cluster` を使用することで、Container Service を環境として使用できます。 [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes) で機械学習モデルを運用化できます。 コンテナー化されたアプリケーションのデプロイ、スケーリング、管理を自動化するために [Kubernetes](https://kubernetes.io/) が使用されます。 このコマンドの実行には約 20 分かかります。 次のコマンドを使用して、デプロイが正常に完了したかどうかを確認します。 

        az ml env show -g [unique]rg -n [unique]

   次のコマンドを実行して、先ほど作成した環境としてデプロイ環境を設定します。

        az ml env set -g [unique]rg -n [unique]

2. モデル管理アカウントを作成して使用します。 モデル管理アカウントを作成するには、次のコマンドを実行します。

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   次のコマンドを実行して、運用化にモデル管理を使用します。

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   モデル管理アカウントは、モデルと Web サービスの管理に使用されます。 Azure Portal から、新しいモデル管理アカウントが作成されたことを確認できます。 このモデル管理アカウントを使用して作成されたモデル、マニフェスト、Docker イメージ、サービスを確認できます。

3. モデルをダウンロードして登録します。

   **fullmodel** コンテナー内のモデルを、ローカル コンピューターのコードのディレクトリにダウンロードします。 "vmlSource.parquet" という名前の parquet データ ファイルはダウンロードしないでください。 これはモデル ファイルではなく、中間計算結果です。 また、Git リポジトリにあるモデル ファイルを再利用することもできます。 詳細については、[GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) を参照してください。 

   CLI で `Model` フォルダーに移動し、次のようにモデルを登録します。

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   各コマンドから、次の手順で必要になるモデル ID が出力されます。 後で使用するためにテキスト ファイルに保存してください。

4. Web サービスのマニフェストを作成します。

   マニフェストには、Web サービス、すべての機械学習モデル、およびランタイム環境の依存関係のコードが含まれます。 CLI で `Code` フォルダーに移動し、次のコマンドを実行します。

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   次の手順で使用されるマニフェスト ID が出力されます。 

   `Code` ディレクトリのままで、次のコマンドを実行して webservice.py をテストできます。 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Docker イメージを作成します。 

        az ml image create -n [unique]image --manifest-id $manifestID

   次の手順で使用するイメージ ID が出力されます。この Docker イメージが Container Service で使用されます。 

6. Web サービスを Container Service クラスターにデプロイします。

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   サービス ID が出力されます。 これを使用して、承認キーとサービス URL を取得する必要があります。

7. Python コードで Web サービスを呼び出して、少量のバッチで評価します。

   次のコマンドを使用して、承認キーを取得します。

         az ml service keys realtime -i $ServiceID 

   次のコマンドを使用して、サービス スコアリング URL を取得します。

        az ml service usage realtime -i $ServiceID

   適切なサービス スコアリング URL と承認キーで `./Config/webservice.json` の内容を変更します。 元のファイルの "ベアラー" を保持し、"xxx" の部分を置き換えます。 
   
   プロジェクトのルート ディレクトリに移動し、次のコマンドを使用して少量のバッチ スコアリングの Web サービスをテストします。

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Web サービスをスケーリングします。 

   詳細については、[Azure Container Service クラスターで運用化をスケールする方法](how-to-scale-clusters.md)に関するページをご覧ください。
 

## <a name="next-steps"></a>次の手順

この例では、Machine Learning Workbench を使用してビッグ データで機械学習モデルをトレーニングし、トレーニングしたモデルを運用化する方法を説明しました。 具体的には、さまざまなコンピューティング ターゲットを構成して使用する方法と、メトリックの追跡履歴を実行し、さまざまな実行を使用する方法について説明しました。

このコードを拡張して、クロス検証とハイパーパラメーター調整を探索できます。 クロス検証とハイパーパラメーター調整の詳細については、[こちらの GitHub リソース](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)を参照してください。  

時系列予測の詳細については、[こちらの GitHub リソース](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)を参照してください。
