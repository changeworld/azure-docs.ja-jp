---
title: 'リファレンス: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Ubuntu Data Science Virtual Machine に含まれるツールの詳細
author: timoklimmer
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: tklimmer
ms.date: 05/12/2021
ms.topic: reference
ms.openlocfilehash: d2a960b2a924d06b7ecc2a2f613fdd5a5695071a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076386"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>リファレンス: Ubuntu (Linux) Data Science Virtual Machine

Ubuntu Data Science Virtual Machine で使用可能なツールの一覧については、以下を参照してください。 

## <a name="deep-learning-libraries"></a>ディープ ラーニング ライブラリ

### <a name="pytorch"></a>PyTorch

[PyTorch](https://pytorch.org/) は、機械学習アルゴリズムを幅広くサポートする一般的な科学コンピューティング フレームワークです。 お使いのマシンに GPU が組み込まれている場合、その GPU を利用してディープ ラーニングを促進できます。PyTorch は `py38_pytorch` 環境内で使用できます。

### <a name="h2o"></a>H2O

H2O は、高速でインメモリの分散型機械学習と予測分析のプラットフォームです。 Python パッケージは、ルートと py35 両方の Anaconda 環境にインストールされます。 また、R パッケージもインストールされます。 

コマンド ラインから H2O を起動するには、`java -jar /dsvm/tools/h2o/current/h2o.jar` を実行します。 多様な[コマンドライン オプション](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)があり、必要に応じて構成できます。 Flow Web UI にアクセスするには、まず `http://localhost:54321` にアクセスします。 サンプルの Notebook は、JupyterHub でも利用できます。

### <a name="tensorflow"></a>TensorFlow

[TensorFlow](https://tensorflow.org) は Google のディープ ラーニング ライブラリです。 データ フロー グラフを使用した数値計算用のオープンソース ソフトウェア ライブラリです。 お使いのマシンに GPU が組み込まれている場合、その GPU を利用してディープ ラーニングを促進できます。 TensorFlow は `py38_tensorflow` conda 環境内で使用できます。


## <a name="python"></a>Python

DSVM には複数の Python 環境が事前にインストールされています。Python バージョンは python 3.8 または Python 3.6 のいずれかです。
インストールされている環境の完全な一覧を表示するには、コマンドラインで `conda env list` を実行します。


## <a name="jupyter"></a>Jupyter

DSVM は、コードと分析を共有するための環境である Jupyter にも付属しています。 Jupyter は、さまざまなフレーバーの DSVM 上にインストールされています。
 - Jupyter Lab
 - Jupyter Notebook
 - Jupyter Hub

Jupyter Lab を開くには、アプリケーション メニューから Jupyter を開くか、デスクトップ アイコンをクリックします。 また、コマンド ラインから `jupyter lab` を実行して Jupyter Lab を開くこともできます。

Jupyter Notebook を開くには、コマンド ラインを開いて、`jupyter notebook` を実行します。

Jupyter Hub を開くには、**https://\<VM DNS name or IP address\>: 8000/** を開きます。 その後、ローカルの Linux ユーザー名とパスワードの入力を求められます。

> [!NOTE]
> 証明書の警告が表示されても続行してください。

> [!NOTE]
> Ubuntu イメージの場合、VM がプロビジョニングされるときに、ポート 8000 は既定でファイアウォール内で開いています。


## <a name="apache-spark-standalone"></a>Apache Spark スタンドアロン

Linux DSVM には Apache Spark のスタンドアロン インスタンスがプレインストールされているため、Spark アプリケーションをローカルで開発し、その後テストして大規模なクラスターにデプロイすることができます。 

Jupyter カーネルを使用して PySpark プログラムを実行できます。 Jupyter を開いて **[New]\(新規\)** ボタンを選択すると、利用可能なカーネルの一覧が表示されます。 **Spark - Python** は、Python 言語を使用して Spark アプリケーションを構築するのに便利な PySpark カーネルです。 また VS.Code や PyCharm などの Python IDE を使用して、ご自身の Spark プログラムを作成することもできます。 

このスタンドアロン インスタンスでは、Spark スタックが呼び出し元のクライアント プログラム内で実行されます。 この機能により、Spark クラスターでの開発に比べて、問題のトラブルシューティングが高速かつ容易になります。


## <a name="ides-and-editors"></a>IDE とエディター

コード エディターには、VS.Code、PyCharm、RStudio、IntelliJ、vi/Vim、Emacs など、複数の選択肢があります。 

VS.Code、PyCharm、RStudio、IntelliJ はグラフィカル エディターです。 使用するには、グラフィカル デスクトップにサインインする必要があります。 これらを起動するには、デスクトップとアプリケーションのメニューのショートカットを使用します。

Vim と Emacs はテキストベースのエディターです。 Emacs の ESS アドオン パッケージにより、Emacs エディター内から R を簡単に利用することができます。 詳細については、[ESS の Web サイト](https://ess.r-project.org/)を参照してください。


## <a name="databases"></a>データベース

### <a name="graphical-sql-client"></a>グラフィカル SQL クライアント

グラフィカルな SQL クライアントである SQuirrel SQL は、さまざまなデータベース (Microsoft SQL Server、MySQL など) に接続し、SQL クエリを実行することができます。 最も簡単に SQuirrel SQL を開くには、(たとえば、X2Go クライアントを使用して) グラフィカル デスクトップ セッションからアプリケーション メニューを使用します

初めて使用する場合は、先にドライバーとデータベースのエイリアスを設定しておく必要があります。 JDBC ドライバーは /usr/share/java/jdbcdrivers にあります。

詳細については、「 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)」をご覧ください。

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Server にアクセスするためのコマンド ライン ツール

SQL Server の ODBC ドライバー パッケージには、次の 2 つのコマンド ライン ツールも付属しています。

- **bcp**:bcp ツールでは、Microsoft SQL Server のインスタンスと、ユーザーが指定した形式のデータ ファイルとの間でデータを一括コピーします。 bcp ツールを使用して、SQL Server テーブルに多数の新しい行をインポートしたり、テーブルからデータ ファイルにデータをエクスポートしたりできます。 テーブルにデータをインポートするには、そのテーブル用に作成されたフォーマット ファイルを使用する必要があります。 または、テーブルの構造を把握し、さらに、その列に有効なデータの種類を把握する必要があります。

詳細については、「 [bcp による接続](/sql/connect/odbc/linux-mac/connecting-with-bcp)」をご覧ください。

- **sqlcmd**:sqlcmd ツールを使用して Transact-SQL ステートメントを入力できます。 または、コマンド プロンプトからシステム プロシージャやスクリプト ファイルを入力することもできます。 このツールは ODBC を使用して、Transact-SQL バッチを実行します。

  詳細については、「 [sqlcmd による接続](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)」をご覧ください。

  > [!NOTE]
  > このツールには、Linux プラットフォームと Windows プラットフォームの間でいくつか違いがあります。 詳細については、 に関するドキュメントを参照してください。

### <a name="database-access-libraries"></a>データベース アクセス ライブラリ

R と Python には、データベースにアクセスするためのライブラリが用意されています。

* R では、RODBC パッケージまたは dplyr パッケージを使用して、データベース サーバーに対してクエリまたは SQL ステートメントを実行することができます。
* Python では、基盤となるレイヤーとして ODBC を使用したデータベース アクセスが pyodbc ライブラリによって実現されます。  

## <a name="azure-tools"></a>Azure ツール

VM には、次の Azure ツールがインストールされています。

* **Azure CLI**:Azure のコマンド ライン インターフェイスから、シェル コマンドを通じて Azure リソースを作成したり管理したりできます。 Azure のツールを開くには、「**azure help**」と入力します。 詳細については、 [Azure CLI ドキュメント ページ](/cli/azure/get-started-with-az-cli2)をご覧ください。
* **Azure Storage Explorer**: Azure Storage Explorer は、Azure ストレージ アカウントに保存されているオブジェクトを参照したり、Azure BLOB との間でデータをアップロードまたはダウンロードしたりするために使用できるグラフィカル ツールです。 ストレージ エクスプローラーには、デスクトップのショートカット アイコンからアクセスできます。 シェル プロンプトから「**StorageExplorer**」と入力して開くこともできます。 X2Go クライアントからサインインしているか、X11 転送を設定済みである必要があります。
* **Azure のライブラリ**: プレインストールされているライブラリの一部を次に示します。
  
  * **Python**: Python の Azure 関連ライブラリには、*azure*、*azureml*、*pydocumentdb*、*pyodbc* があります。 最初の 3 つのライブラリを使用すると、Azure Storage サービス、Azure Machine Learning、Azure Cosmos DB (Azure の NoSQL データベース) にアクセスできます。 4 番目のライブラリである pyodbc を (Microsoft ODBC Driver for SQL Server と共に) では、ODBC インターフェイスを使用して、Python から SQL Server、Azure SQL Database、Azure Synapse Analytics にアクセスできます。 すべてのライブラリを一覧表示するには、「 **pip list** 」と入力します。 このコマンドは、必ず Python 2.7 と 3.5 の両方の環境で実行してください。
  * **R**:R の Azure 関連ライブラリは、AzureML と RODBC です。
  * **Java**: Azure Java ライブラリのリストは、VM の /dsvm/sdk/AzureSDKJava ディレクトリにあります。 主要なライブラリは、Azure Storage と管理 API、Azure Cosmos DB、SQL Server の JDBC ドライバーです。  

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning は、予測分析ソリューションを構築、デプロイ、共有できる、フル マネージドのクラウド サービスです。 実験やモデルは、Azure Machine Learning Studio (プレビュー) で作成できます。 Azure Machine Learning Studio にアクセスするには、Data Science Virtual Machine 上の Web ブラウザーから [Microsoft Azure Machine Learning](https://ml.azure.com) にアクセスします。

Azure Machine Learning Studio にサインインしたら、実験キャンバスを使用して機械学習アルゴリズムの論理フローを作成することができます。 また、Azure Machine Learning でホストされている Jupyter Notebook にアクセスし、Azure Machine Learning Studio 内の実験をシームレスに操作することもできます。 

作成した機械学習モデルを Web サービス インターフェイスにラップすることで、モデルを運用可能な状態にすることができます。 機械学習モデルを運用可能な状態にすると、任意の言語で記述されたクライアントで、機械学習モデルから予測を呼び出すことができます。 詳細については、[Machine Learning のドキュメント](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

VM で R または Python を使ってモデルを作成し、Azure Machine Learning の運用環境にデプロイすることもできます。 この機能を有効にするために、R (**AzureML**) と Python (**azureml**) にライブラリがインストールされています。

> [!NOTE]
> これらの手順は、Windows バージョンの Data Science Virtual Machine 向けに書かれたものです。 ただし、これらに記載されている Azure Machine Learning へのモデルのデプロイに関する情報は Linux VM にも適用されます。

## <a name="machine-learning-tools"></a>機械学習ツール

VM には、プリコンパイルされてローカルにプレインストールされた機械学習ツールとアルゴリズムが備わっています。 これには以下が含まれます。

* **Vowpal Wabbit**: 高速オンライン学習アルゴリズム。
* **xgboost**: 最適化されたブースト ツリー アルゴリズムを提供するツール。
* **Rattle**: データ探索とモデリングに役立つ R ベースのグラフィカル ツール。
* **Python**: Anaconda Python には、Scikit-learn などのライブラリと機械学習アルゴリズムがバンドルされています。 その他のライブラリは、 `pip install` を使用してインストールできます。
* **LightGBM**: デシジョン ツリー アルゴリズムに基づく、高速で分散型の高パフォーマンスな勾配ブースティング フレームワーク。
* **R**:R 向けに、機械学習関数の豊富なライブラリが用意されています。プレインストールされているライブラリには、lm、glm、randomForest、rpart などがあります。 その他のライブラリは、次のコマンドを使用してインストールできます。

    ```r
    install.packages(<lib name>)
    ```

上記の最初の 3 つの機械学習ツールに関する追加情報を次に示します。

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit は、オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法を使用する機械学習システムです。

基本的な例でこのツールを実行するには、次のコマンドを使用します。

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

このディレクトリには、ほかにも多数のデモが用意されています。 Vowpal Wabbit の詳細については、[GitHub のこちらのセクション](https://github.com/JohnLangford/vowpal_wabbit)と [Vowpal Wabbit の Wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) を参照してください。

### <a name="xgboost"></a>xgboost

xgboost は、ブースト (ツリー) アルゴリズム向けに設計および最適化されたライブラリです。 このライブラリの目的は、コンピューターの計算能力の限界を超えて、スケーラブルで移植できる正確で大規模なツリー ブーストを提供することです。

コマンド ラインおよび R ライブラリとして提供されています。 R でこのライブラリを使用するには、(シェルで「**R**」と入力して) 対話型の R セッションを開始し、ライブラリを読み込みます。

R プロンプトで実行できる簡単な例を次に示します。

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

xgboost コマンド ラインを実行するには、シェルで次のコマンドを実行します。

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model ファイルは、指定したディレクトリに書き込まれます。 このデモの例については、[GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification) でご覧いただけます。

xgboost の詳細については、[xgboost ドキュメント ページ](https://xgboost.readthedocs.org/en/latest/)および xgboost の [GitHub リポジトリ](https://github.com/dmlc/xgboost)を参照してください。

### <a name="rattle"></a>Rattle

Rattle (**R** **A** nalytical **T** ool **T** o **L** earn **E** asily) では、GUI ベースのデータ探索とモデリングを使用します。 Rattle では、データの統計の概要を視覚的に表示し、簡単にモデル化できるようにデータを変換します。データから教師なしと教師ありの両方のモデルを作成し、モデルのパフォーマンスをグラフィカルに表示して、新しいデータセットをスコア付けします。 また、UI での操作をレプリケートする R コードを生成することもできます。生成したコードは、R で直接実行することや、詳細な分析の開始点として使用することができます。

Rattle を実行するには、グラフィカル デスクトップのサインイン セッションに入っている必要があります。 ターミナルで「**R**」と入力して、R 環境を開きます。 R プロンプトで、次のコマンドを入力します。

```R
library(rattle)
rattle()
```

これで、グラフィカル インターフェイスが開き、一連のタブが表示されます。 次のクイックスタート手順に従い、Rattle でサンプルの天候データ セットを使用してモデルを構築します。 一部の手順では、必要な R パッケージのうち、システム上にないものを自動的にインストールして読み込むことを確認するメッセージが表示されます。

> [!NOTE]
> システム ディレクトリ (既定) にパッケージをインストールするためのアクセス許可がない場合、R コンソール ウィンドウに、個人用ライブラリにパッケージをインストールするように求めるメッセージが表示されます。 そのメッセージが表示された場合は、 **y** で回答します。

1. **[実行]** を選択します。
1. サンプルの天候データ セットを使用するかどうかをたずねるダイアログ ボックスが表示されます。 **[Yes]\(はい\)** を選択して、サンプルを読み込みます。
1. **[Model]\(モデル\)** タブを選択します。
1. **[Execute]\(実行\)** を選択して、デシジョン ツリーを作成します。
1. **[Draw]\(表示\)** を選択して、デシジョン ツリーを表示します。
1. **[Forest]\(フォレスト\)** オプションをオンにし、 **[Execute]\(実行\)** を選択してランダム フォレストを作成します。
1. **[Evaluate]\(評価\)** タブを選択します。
1. **[Risk]\(リスク\)** オプションをオンにし、 **[Execute]\(実行\)** を選択して、2 つの **リスク (累積)** パフォーマンス プロットを表示します。
1. **[Log]\(ログ\)** タブを選択して、これまでの操作の生成済み R コードを表示します
   (Rattle の現在のリリースにはバグがあるため、ログのテキストの **Export this log** の前に **#** 文字を挿入する必要があります)。
1. **[Export]\(エクスポート\)** ボタンを選択して、R スクリプト ファイルを *weather_script.R* という名前でホーム フォルダーに保存します。

Rattle と R を終了できます。これで、生成された R スクリプトに変更を加えることができます。 または、スクリプトをそのまま使用することもできます。スクリプトをいつでも実行して、Rattle UI 内で実行されたすべての操作を繰り返すことができます。 これは、特に R の初心者向けの方法です。この方法では、シンプルなグラフィカル インターフェイスで分析と機械学習をすばやく実行することができ、変更または学習する R のコードを自動的に生成できます。

## <a name="next-steps"></a>次のステップ

その他の質問がある場合は、 [サポート チケット](https://azure.microsoft.com/support/create-ticket/)の作成を検討してください。