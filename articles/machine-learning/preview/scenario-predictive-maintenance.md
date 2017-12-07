---
title: "予測メンテナンスの実際のシナリオ | Microsoft Docs"
description: "PySpark を使用した予測メンテナンスの実際のシナリオ"
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
ms.openlocfilehash: 2687eb022bce0b71c217f0be611c8fabdfb66040
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>予測メンテナンスの実際のシナリオ

予期しない設備のダウンタイムは、どのような企業にとっても損害になる可能性があります。 そのため、使用率やパフォーマンスを最大化するには、またコストのかかる予定外のダウンタイムを最小化するには、現場の設備を稼働し続けることが必須です。 問題を早期に特定することで、コスト効率の高い方法で限られた保守リソースをデプロイし、品質管理やサプライ チェーンのプロセスを向上させることができます。 

このシナリオでは、比較的[大規模なデータ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)を使用して、データの取り込み、特徴エンジニアリング、モデルの構築の手順を案内し、最後にモデルの運用化とデプロイについて説明します。 プロセス全体のコードは、PySpark で記述され、Azure ML Workbench で Jupyter ノートブックを使用して実装されています。 最終的に最適なモデルは Azure Machine Learning モデル管理を使用して運用化され、運用環境でリアルタイムの障害予測を行うために使用されます。   

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

パブリック GitHub リポジトリのリンクは [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) です


## <a name="use-case-overview"></a>ユース ケースの概要

資産の量が多い業種の企業が直面する主な問題は、機械的な問題による遅延に関係する重大なコストです。 ほとんどの企業は、このような問題が生じる前に事前に防ぐために、問題が発生するタイミングの予測に関心があります。 目標は、ダウンタイムを短縮してコストを削減し、できる限り安全性を高めることです。 予測メンテナンスに使用される一般的なユース ケースとモデリング アプローチの詳細な説明については、[予測メンテナンスのプレイブック](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)に関するページを参照してください。

このシナリオでは、複数の実際の問題の複合に基づくシナリオに予測モデルを実装する手順を提供する目的で、プレイブックのアイデアを利用します。 次の例は、多くの予測メンテナンスのユース ケースで見られる一般的なデータ要素をまとめたものです。

このシミュレートされたデータのビジネスの問題は、コンポーネント エラーで発生する問題を予測することです。 そのため、ビジネスの質問は、"*コンポーネント エラーが原因でマシンが停止する可能性はどのくらいか*" です。 この問題は、多クラス分類 (1 つのマシンにつき複数のコンポーネント) の問題として構成され、予測モデルを作成するために機械学習アルゴリズムが使用されます。 モデルは、マシンから収集された履歴データに対してトレーニングされます。 このシナリオでは、Azure Machine Learning Workbench 環境内のこのようなモデルを実装する多様な手順を実行します。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/en-us/free/) (無料試用版もご利用いただけます)。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のインストール済みコピー。[クイックスタート インストール ガイド](./quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成します。
* Azure Machine Learning Operationalization は、ローカル デプロイ環境と[管理アカウント](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)を必要とします

この例は、任意の AML Workbench コンピューティング コンテキストで実行できます。 ただし、少なくとも 16 GB のメモリを備えたマシン上で実行することをお勧めします。 このシナリオは、リモート DS4_V2 標準 [Linux 用データ サイエンス仮想マシン (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) を実行している Windows 10 マシンで構築され、テストされました。

モデルの運用化は、Azure ML CLI のバージョン 0.1.0a22 を使用して実行されました。

## <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Predictive Maintenance」と入力し、テンプレートを選択します
5.  **[作成]**

## <a name="prepare-the-notebook-server-computation-target"></a>ノートブック サーバーの計算ターゲットの準備

ローカル コンピューター上で実行するには、AML Workbench の `File` メニューの `Open Command Prompt` または `Open PowerShell CLI` を選択します。 CLI ウィンドウ内で、次のコマンドを実行します。

`az ml experiment prepare --target docker --run-configuration docker`

Linux 用データ サイエンス仮想マシン (Ubuntu) 上で実行することをお勧めします。 DSVM が構成されたら、次の 2 つのコマンドを実行します。

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

docker イメージを準備した状態で、Jupyter ノートブック サーバーを AML Workbench のノートブック タブ内で開くか、`az ml notebook start` を実行してブラウザーベースのサーバーを起動します。

ノートブックは、Jupyter 環境内の `Code` ディレクトリに格納されています。 ノートブックは、最初の (`Code\1_data_ingestion.ipynb`) ノートブックから順番に実行されます。 各ノートブックを開くとき、計算カーネルを選択するように求められます。 [プロジェクト名]_Template [目的の接続名] を選択し、[Set Kernel]\(カーネルの設定\) をクリックします。

## <a name="data-description"></a>データの説明

[シミュレートされたデータ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)は、5 個のコンマ区切り値 (.csv) ファイルで構成されます。 データ セットの詳細な説明については、リンク先のページを参照してください。

* [マシン](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 各マシンを区別する特徴。 たとえば、製造年、モデルなどです。
* [エラー](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): エラー ログには、マシンが動作したままの状態でスローされる重要ではないエラーが含まれています。 これらのエラーは障害として見なされませんが、将来的な障害イベントの予測になる可能性があります。 テレメトリ データは、1 時間間隔で収集されるため、エラー日時は最も近い時刻に丸められます。
* [メンテナンス](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): メンテナンス ログには、スケジュールされたメンテナンス レコードとスケジュールされていないメンテナンス レコードの両方が含まれています。 スケジュールされたメンテナンスは、コンポーネントの通常の検査に対応し、スケジュールされていないメンテナンスは、機械的な障害または他のパフォーマンス低下から生じる可能性があります。 テレメトリ データは、1 時間間隔で収集されるため、メンテナンス日時は最も近い時刻に丸められます。
* [テレメトリ](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): テレメトリの時系列データは、リアルタイムで各マシンから収集された電圧、回転、負荷、および振動センサーの測定で構成されます。 データは 1 時間の平均が計算され、テレメトリ ログに保存されます。
* [障害](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 障害は、メンテナンス ログ内のコンポーネントの置き換えに対応します。 各レコードには、メンテナンス ID、コンポーネントの種類、置き換えの日時が含まれています。 これらのレコードは、モデルが予測しようとする機械学習ラベルを作成するために使用されます。

Jupyter ノートブック シナリオの[データの取り込み](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)に関するページのコード セクションを参照して、GitHub リポジトリの生データ セットをダウンロードし、この分析のために PySpark データセットを作成します。

## <a name="scenario-structure"></a>シナリオの構造
シナリオの内容については、[GitHub リポジトリ](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)を参照してください。 

リポジトリには、データの準備から、いくつかのモデルを構築し、最終的に最適なモデルの 1 つを運用化するまでのプロセスの概要について説明した [Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) ファイルがあります。 4 つの Jupyter ノートブックをリポジトリ内の [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) フォルダーで使用できます。   

次に、手順のシナリオ ワークフローについて説明します。 エンド ツー エンドのシナリオは PySpark で記述され、4 つのノートブックに分割されています。

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): このノートブックは 5 つの入力 .csv ファイルをダウンロードし、いくつかの事前データ クリーンアップと視覚化を実行します。 このノートブックは、データを PySpark 形式に変換し、特徴エンジニアリング タスクで使用できるようにその結果を Azure BLOB コンテナーに保存します。

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): 前の手順でクリーニングされたデータセットを使用し、テレメトリ センサーのラグおよび集約された特徴が作成されます。さらに、エラー数、コンポーネントの置き換え、マシン情報がテレメトリ データに結合されます。 障害に関連するコンポーネントの置き換えは、障害が発生したコンポーネントを記述するラベルを作成するために使用されます。 ラベル付きの特徴データは、モデル構築タスクのために Azure BLOB に保存されます。

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): モデリング ノートブックは、ラベル付きの特徴データセットを使用して、日付時刻スタンプに沿ってトレーニング用と開発用のデータセットにデータを分割します。 このノートブックは、`pyspark.ml.classification` モデルを使用した設定セット実験です。 トレーニング データはベクトル化されます。ユーザーは、`DecisionTreeClassifier` または `RandomForestClassifier` のいずれかを使用して実験し、ハイパーパラメーターを操作して最高のパフォーマンスを発揮するモデルを見つけることができます。 パフォーマンスは、開発用のデータセットに対する測定統計情報を評価することによって判定されます。 これらの統計は、追跡のために AML Workbench の実行時画面に再び記録されます。 実行のたびに、ノートブックは、Jupyter ノートブック カーネルを実行しているローカル ディスクに結果のモデルを保存します。 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): このノートブックは、ローカル (Jupyter ノートブック カーネル) ディスクに保存された最後のモデルを使用して、モデルを Azure Web サービスに運用化するためのコンポーネントを構築します。 完全な運用資産は、別の Azure BLOB コンテナーに格納されている `o16n.zip` ファイルに圧縮されています。 圧縮ファイルには、次のものが含まれます。

* `service_schema.json`: デプロイ用のスキーマ定義ファイル。 
* `pdmscore.py`: Azure Web サービスに必要な init() 関数と run() 関数
* `pdmrfull.model`: モデル定義ディレクトリ。
    
 このノートブックは、デプロイのために運用資産をパッケージ化する前に、モデル定義を使用して関数をテストします。 デプロイの手順は、ノートブックの末尾に記載されています。

## <a name="conclusion"></a>まとめ

このシナリオを読むと、Azure ML Workbench の Jupyter ノートブック環境内で PySpark を使用して、エンド ツー エンドの予測メンテナンス ソリューションを構築する方法の概要を理解できます。 また、運用環境でリアルタイムの障害予測に使用するために、Azure Machine Learning モデル管理環境を使用して最適なモデルを簡単に運用化してデプロイする方法についても案内しています。 また、ビジネスのニーズに合わせてシナリオの関連する部分を編集することができるようになります。  

## <a name="references"></a>参照

このユース ケースは、以前は複数のプラットフォームで開発されていました。

* [予測メンテナンス ソリューション テンプレート](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [予測メンテナンスのモデリング ガイド](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R サービスを使用した予測メンテナンス モデリング ガイド](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [予測メンテナンス モデリング ガイド Python ノートブック](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark を使用する予測メンテナンス](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>次のステップ

これ以外にも、Azure Machine Learning Workbench 内で利用できる、製品の他の機能を示す多くのサンプル シナリオがあります。 