---
title: 現実的なシナリオに対応した予測メンテナンス | Microsoft Docs
description: PySpark を使用した現実的なシナリオに対応した予測メンテナンス
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: bb6e4f9f147db2fb70d991922cf0bb8d16b29671
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>現実的なシナリオに対応した予測メンテナンス

予期しない設備のダウンタイムは、どのような企業にとっても損害になる可能性があります。 使用率やパフォーマンスを最大化し、コストのかかる予定外のダウンタイムを最小化するには、現場の設備を稼働し続けることが必須です。 問題を早期に特定することで、コスト効率の高い方法で限られた保守リソースをデプロイし、品質管理やサプライ チェーンのプロセスを向上させることができます。 

このシナリオでは、比較的[大規模なシミュレートされたデータ セット](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)を使用して、予測メンテナンスのデータ サイエンス プロジェクト (データ インジェスト、特徴エンジニアリング、モデルの構築、モデルの運用化とデプロイ) について説明します。 プロセス全体のコードは、Azure Machine Learning Workbench で PySpark を使用して、Jupyter Notebook に記述されています。 最終的なモデルは、設備の障害をリアルタイムで予測するために、Azure Machine Learning モデル管理を使用してデプロイされます。   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence ギャラリーの GitHub リポジトリ

PM チュートリアルの Cortana Intelligence ギャラリーはパブリック GitHub リポジトリ ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) です。ここで問題の報告や投稿を行うことができます。


## <a name="use-case-overview"></a>ユース ケースの概要

資産の量が多い業種の企業が直面する主な問題は、機械的な問題による遅延に関係する重大なコストです。 ほとんどの企業は、このような問題が生じる前に事前に防ぐために、問題が発生するタイミングの予測に関心があります。 目標は、ダウンタイムを短縮してコストを削減し、できる限り安全性を高めることです。 

このシナリオでは、シミュレートされたデータ セット用の予測モデルの構築方法を示す目的で、[予測メンテナンス プレイブック](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)のアイデアを利用します。 サンプルのデータは、多くの予測メンテナンスのユース ケースで見られる一般的な要素から得られたものです。

このシミュレートされたデータのビジネスの問題は、コンポーネント エラーで発生する問題を予測することです。 ビジネスの質問は、"*コンポーネント エラーが原因でマシンが停止する可能性はどのくらいか*" です。 この問題は、多クラス分類の問題 (コンピューターごとの複数のコンポーネント) としてフォーマットされます。 予測モデルの作成には、機械学習アルゴリズムが使用されます。 モデルは、マシンから収集された履歴データに対してトレーニングされます。 このシナリオでは、さまざまな手順を実行して、Machine Learning Workbench 環境内にモデルを実装します。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版も使用できます)。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のインストール済みコピー。 プログラムをインストールし、ワークスペースを作成するには、[クイックスタート インストール ガイド](./quickstart-installation.md)を参照してください。
* Azure Machine Learning Operationalization には、ローカル デプロイ環境と [Azure Machine Learning モデル管理アカウント](model-management-overview.md)が必要です。

この例は、任意の Machine Learning Workbench コンピューティング コンテキストで実行できます。 ただし、少なくとも 16 GB のメモリを使用して実行することをお勧めします。 このシナリオは、リモート DS4_V2 標準 [Linux (Ubuntu) 用データ サイエンス仮想マシン (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) を実行している Windows 10 マシンで構築され、テストされました。

モデルの運用化は、Azure Machine Learning CLI のバージョン 0.1.0a22 を使用して実行されました。

## <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Machine Learning Workbench を開きます。
2.  **[プロジェクト]** ページで **[+]** をクリックし、**[新しいプロジェクト]** を選択します。
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「予測メンテナンス」と入力し、**予測メンテナンス** テンプレートを選択します。
5.  **[作成]**を選択します。

## <a name="prepare-the-notebook-server-computation-target"></a>ノートブック サーバーの計算ターゲットの準備

ローカル マシンで実行するには、Machine Learning Workbench の **[ファイル]** メニューから **[コマンド プロンプトを開く]** または **[Open PowerShell CLI]\(PowerShell CLI を開く\)** のいずれかを選択します。 CLI インターフェイスを使用すると、`az` コマンドを実行して Azure サービスにアクセスできます。 最初に、次のコマンドを使用して Azure アカウントにログインします。

```
az login
``` 

このコマンドは、URL https:\\aka.ms\devicelogin で使用する認証キーを提供します。 CLI は、デバイスのログイン操作が戻り、接続情報が提供されるまで待機します。 続いて、ローカルの [Docker](https://www.docker.com/get-docker) インストールがある場合は、次のコマンドを使用してローカルのコンピューティング環境を準備します。

```
az ml experiment prepare --target docker --run-configuration docker
```

メモリとディスクの要件に対応するために、[Linux (Ubuntu) 用 DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) で実行することをお勧めします。 DSVM の構成が完了したら、次の 2 つのコマンドを使用してリモートの Docker 環境を準備します。

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

リモートの Docker コンテナーに接続したら、次のコマンドを使用して DSVM の Docker コンピューティング環境を準備します。 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Docker コンピューティング環境を準備した状態で、Azure Machine Learning Workbench の **[Notebooks]\(ノートブック\)** タブから Jupyter Notebook サーバーを開くか、次のコマンドを実行してブラウザーベースのサーバーを起動します。 

```
az ml notebook start
```

サンプルの Notebook は、Code ディレクトリに格納されています。 Notebook は、最初の (Code\1_data_ingestion.ipynb) Notebook から順番に実行するように設定されます。 各 Notebook を開くとき、計算カーネルを選択するように求められます。 [Project_Name]_Template [Connection_Name] を選択して、以前に構成された DSVM で実行します。

## <a name="data-description"></a>データの説明

[シミュレートされたデータ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)は、5 個のコンマ区切り値 (.csv) ファイルで構成されます。 データ セットに関する詳細な説明を取得するには、次のリンクを使用してください。

* [マシン](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 各コンピューターを区別するための特長 (経過時間やモデルなど)。
* [エラー](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): エラー ログには、マシンが動作したままの状態でスローされる重要ではないエラーが含まれています。 これらのエラーは障害と見なされませんが、将来的な障害イベントの予測になる可能性があります。 テレメトリ データは、1 時間間隔で収集されるため、エラーの日時値は最も近い時刻に丸められます。
* [メンテナンス](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): メンテナンス ログには、スケジュールされたメンテナンス レコードとスケジュールされていないメンテナンス レコードの両方が含まれています。 スケジュールされたメンテナンスは、コンポーネントの定期検査に対応します。 機械的な障害やその他のパフォーマンス低下によって、予定外のメンテナンスが発生することもあります。 テレメトリ データは、1 時間間隔で収集されるため、メンテナンスの日時値は最も近い時刻に丸められます。
* [テレメトリ](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): テレメトリ データは、各マシン内の複数のセンサーからの時系列の測定で構成されます。 センサーの値の 1 時間の平均を計算して、このデータがログに記録されます。
* [障害](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 障害は、メンテナンス ログ内のコンポーネントの置き換えに対応します。 各レコードには、メンテナンス ID、コンポーネントの種類、置き換えの日時が含まれています。 これらのレコードは、モデルが予測しようとする機械学習ラベルを作成するために使用されます。

GitHub リポジトリの生データ セットをダウンロードし、この分析のために PySpark データセットを作成するには、Code フォルダーにある[データ インジェスト](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook シナリオを参照してください。

## <a name="scenario-structure"></a>シナリオの構造
シナリオの内容については、[GitHub リポジトリ](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)を参照してください。 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) ファイルは、ワークフロー (データの準備、モデルの構築、運用環境用のソリューションのデプロイ) の概要を示します。 ワークフローの各手順は、リポジトリ内の [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) フォルダーにある Jupyter Notebook にカプセル化されます。   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): このノートブックは 5 つの入力 .csv ファイルをダウンロードし、いくつかの事前データ クリーンアップと視覚化を実行します。 このノートブックは、各データ セットを PySpark 形式に変換し、特徴エンジニアリング ノートブックで使用できるように Azure BLOB コンテナーに保存します。

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): モデルの特徴は、テレメトリ、エラー、およびメンテナンス データに対する標準の時系列アプローチを使用して、Azure BLOB Storage の生データセットから構築されます。 障害に関連するコンポーネントの置き換えは、障害が発生したコンポーネントを記述するモデルのラベルを作成するために使用されます。 ラベル付きの特徴データは、モデル構築 Notebook のために Azure BLOB に保存されます。

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): モデル構築 Notebook は、ラベル付きの特徴データ セットを使用し、データ セットを日時タイムスタンプと共にトレーニング データ セットと開発データ セットに分割します。 Notebook は pyspark.ml.classification モデルを使った実験として設定されます。 トレーニング データはベクター化されます。 ユーザーは、**DecisionTreeClassifier** または **RandomForestClassifier** のいずれかを使用して実験を行い、ハイパーパラ メーターを操作して、最もパフォーマンスの良いモデルを特定することができます。 パフォーマンスは、開発用のデータセットに対する測定統計情報を評価することによって判定されます。 これらの統計は、追跡のために Machine Learning Workbench の実行時画面に再び記録されます。 実行のたびに、ノートブックは、Jupyter Notebook カーネルを実行しているローカル ディスクに結果のモデルを保存します。 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): このノートブックは、ローカル (Jupyter Notebook カーネル) ディスクに保存された最後のモデルを使用して、モデルを Azure Web サービスにデプロイするためのコンポーネントを構築します。 完全な運用資産は、別の Azure BLOB コンテナーに格納されている o16n.zip ファイルに圧縮されています。 圧縮ファイルには、次のものが含まれます。

* **service_schema.json**: デプロイ用のスキーマ定義ファイル。 
* **pdmscore.py**: Azure Web サービスによって要求される **init()** 関数と **run()** 関数。
* **pdmrfull.model**: モデル定義ディレクトリ。
    
このノートブックは、デプロイのために運用資産をパッケージ化する前に、モデル定義を使用して関数をテストします。 デプロイの手順は、ノートブックの末尾に記載されています。

## <a name="conclusion"></a>まとめ

このシナリオでは、Machine Learning Workbench の Jupyter Notebook 環境内で PySpark を使用して、エンド ツー エンドの予測メンテナンス ソリューションを構築する方法について概要しています。 このサンプル シナリオでは、設備の障害をリアルタイムで予測するための、Machine Learning モデル管理環境を使用したモデルのデプロイについても詳しく説明しています。

## <a name="references"></a>参照

次の参照資料では、各種のプラットフォームを対象とした、その他の予測メンテナンス ユース ケースの例が示されています。

* [予測メンテナンス ソリューション テンプレート](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [予測メンテナンスのモデリング ガイド](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R サービスを使用した予測メンテナンス モデリング ガイド](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [予測メンテナンス モデリング ガイド Python ノートブック](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark を使用する予測メンテナンス](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [予測メンテナンスのためのディープ ラーニング](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>次の手順

これ以外にも、Machine Learning Workbench 内で利用できる、製品の他の機能を示す多くのサンプル シナリオがあります。 
