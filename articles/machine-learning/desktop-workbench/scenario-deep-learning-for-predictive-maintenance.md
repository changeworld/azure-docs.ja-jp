---
title: 予測メンテナンスのためのディープ ラーニングの実際のシナリオ | Microsoft Docs
description: 予測メンテナンスのためのディープ ラーニングに関するチュートリアルを Azure Machine Learning Workbench で複製する方法を説明します。
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 26b061c6bd6ff3ec9d1edbb7d99053d17db9b773
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832603"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>予測メンテナンスのためのディープ ラーニングの実際のシナリオ

ディープ ラーニングは、機械学習における最も一般的なトレンドの 1 つであり、次のような多くの分野に使用されています。
- 無人自動車とロボット工学
- 音声認識と画像認識
- 財務予測

ディープ ニューラル ネットワーク (DNN) とも呼ばれるこれらの方法は、脳 (生物学的神経回路網) 内にある個別のニューロンから着想を得ています。

予期しない設備のダウンタイムは、どのような企業にとっても損害になる可能性があります。 使用率やパフォーマンスを最大化し、コストのかかる予定外のダウンタイムを最小化するには、現場の設備を稼働し続けることが必須です。 問題を早期に特定することで、コスト効率の高い方法で限られた保守リソースをデプロイし、品質管理やサプライ チェーンのプロセスを向上させることができます。 

予測メンテナンス (PM) 戦略では、機械学習の方法を使用して、事前にメンテナンスを実行するために設備の状態を判断し、有害なマシンのパフォーマンスを防ぎます。 PM では、データを長期間収集して分析し、マシンの状態を監視した後、それらを分析して障害予測のためのパターンを見つけます。 [短期および長期のメモリ (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) ネットワークは、データのシーケンスから学習するために設計されるため、この設定にとって魅力的です。

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence ギャラリーの GitHub リポジトリ

PM チュートリアルの Cortana Intelligence ギャラリーはパブリック GitHub リポジトリ ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) です。ここで問題の報告や投稿を行うことができます。


## <a name="use-case-overview"></a>ユース ケースの概要

このチュートリアルでは、シミュレートされた航空エンジンの run-to-failure イベントの例を使用して、予測メンテナンス モデリング プロセスを示します。 このシナリオは、[予測メンテナンス](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)で説明されます。

この設定の主な前提事項は、エンジンが有効期間のうちに徐々に低下することです。 この低下は、エンジン センサー測定で検出できます。 PM では、これらのセンサーの値の変化と障害の履歴の関係をモデル化しようとします。 その後、このモデルによって、センサー測定の現在の状態に基づいて、今後にエンジンで障害が発生する時期を予測できます。

このシナリオでは、LSTM ネットワークを作成し、センサーの値の履歴を使用して、航空エンジンの残存耐用年数 (RUL) を予測します。 このシナリオでは、[Tensorflow](https://www.tensorflow.org/) ディープ ラーニング フレームワークをコンピューティング エンジンとして使用した、[Keras](https://keras.io/) ライブラリを使用します。 このシナリオでは、一連のエンジンを使用して LSTM をトレーニングし、未知のエンジン セットでネットワークをテストします。

## <a name="prerequisites"></a>前提条件
- [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版も使用できます)。
- ワークスペース作成済みの Azure Machine Learning Workbench。
- モデルを運用化するために: ローカル デプロイ環境がセットアップされた Azure Machine Learning Operationalization と [Azure Machine Learning モデル管理アカウント](model-management-overview.md)。

## <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。

1. Machine Learning Workbench を開きます。
2. **[プロジェクト]** ページで **[+]** をクリックし、**[新しいプロジェクト]** を選択します。
3. **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4. **[プロジェクト テンプレートの検索]** 検索ボックスに「予測メンテナンス」と入力し、**予測メンテナンスのためのディープ ラーニング シナリオ** テンプレートを選択します。
5. **[作成]** を選択します。

## <a name="prepare-the-notebook-server-computation-target"></a>ノートブック サーバーの計算ターゲットの準備

ローカル マシンで実行するには、Machine Learning Workbench の **[ファイル]** メニューから **[コマンド プロンプトを開く]** または **[Open PowerShell CLI]\(PowerShell CLI を開く\)** のいずれかを選択します。 CLI インターフェイスを使用すると、`az` コマンドを実行して Azure サービスにアクセスできます。 最初に、次のコマンドを使用して Azure アカウントにログインします。

```
az login
``` 

このコマンドは、URL https:\\aka.ms\devicelogin で使用する認証キーを提供します。 CLI は、デバイスのログイン操作が戻り、接続情報が提供されるまで待機します。 続いて、ローカルの [Docker](https://www.docker.com/get-docker) インストールがある場合は、次のコマンドを使用してローカルのコンピューティング環境を準備します。

```
az ml experiment prepare --target docker --run-configuration docker
```

メモリとディスクの要件に対応するために、[Linux (Ubuntu) 用データ サイエンス仮想マシン(DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) で実行することをお勧めします。 DSVM の構成が完了したら、次の 2 つのコマンドを使用してリモートの Docker 環境を準備します。

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

リモートの Docker コンテナーに接続したら、次のコマンドを使用して DSVM の Docker コンピューティング環境を準備します。 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Docker コンピューティング環境を準備した状態で、Machine Learning Workbench の **[Notebooks]\(ノートブック\)** タブからJupyter Notebook サーバーを開くか、次のコマンドを実行してブラウザーベースのサーバーを起動します。 

```
az ml notebook start
```

サンプルの Notebook は、Code ディレクトリに格納されています。 Notebook は、最初の (Code\1_data_ingestion.ipynb) Notebook から順番に実行するように設定されます。 各 Notebook を開くとき、計算カーネルを選択するように求められます。 [Project_Name]_Template [Connection_Name] を選択して、以前に構成された DSVM で実行します。

## <a name="data-description"></a>データの説明

テンプレートでは、PM_train.txt、PM_test.txt、および PM_truth.txt というファイル内の 3 つのデータセットを入力として使用します。 

- **トレーニング データ**: 航空エンジンの run-to-failure データ。 トレーニング データ (PM_train.txt) は、複数の多変量の時系列で構成され、時間単位として "*サイクル*" が使用されています。 これには、サイクルごとに 21 個のセンサーの読み取り値が含まれます。 

    - 各時系列は、同じ型の異なるエンジンから生成されます。 各エンジンは、程度が異なる初期摩耗と、製造時の固有のばらつきを持って開始されます。 ユーザーはこの情報を知りません。 

    - このシミュレートされたデータでは、エンジンは、各時系列の開始時には正常に動作するものとします。 それは、一連の運転サイクル中のある時点で劣化し始めます。 劣化は進行して規模が大きくなっていきます。 

    - 定義済みのしきい値に達すると、エンジンはこれ以上運転するのは安全でないとみなされます。 各時系列の最後のサイクルは、そのエンジンの障害点です。

- **テスト データ**: 障害イベントの記録がない航空エンジンの運転データ。 テスト データ (PM_test.txt) のデータ スキーマは、トレーニング データと同じです。 唯一の違いは、このデータはいつ障害が発生するかを示さないことです (最後の期間は障害点を*表わしません*)。 障害が発生する前にエンジンが持ちこたえるサイクル数は不明です。

- **実データ**: テスト データ内の各エンジンの実際の残存サイクルの情報です。 基礎となる実データは、テスト データ内のエンジンの残存動作サイクル数を示します。

## <a name="scenario-structure"></a>シナリオの構造

シナリオのワークフローは 3 つの手順に分かれていて、各手順は Jupyter Notebook で実行されます。 各 Notebook では、Notebook 用にローカルで保存されるデータ アーティファクトが生成されます。

### <a name="task-1-data-ingestion-and-preparation"></a>タスク 1: データの取り込みと準備

Code/1_data_ingestion_and_preparation.ipnyb のデータ インジェスト Jupyter Notebook が、3 つの入力データ セットを Pandas データフレーム形式に読み込みます。 Notebook は次に、モデリング用にデータを準備し、何らかの予備的なデータの視覚化を実行します。 データ セットは、モデル構築 Jupyter Notebook 用に、コンピューティング コンテキストに対してローカルに格納されます。

### <a name="task-2-model-building-and-evaluation"></a>タスク 2: モデルのビルドと評価

Code/2_model_building_and_evaluation.ipnyb 内のモデル構築 Jupyter Notebook では、ディスクからトレーニングとテストのデータ セットを読み取り、トレーニング データ セット用の LSTM ネットワークをビルドします。 テスト データ セットに対するモデルのパフォーマンスが測定されます。 結果モデルがシリアル化され、運用化タスクで使用するためにローカル コンピューティング コンテキストに格納されます。

### <a name="task-3-operationalization"></a>タスク 3: 運用化

Code/3_operationalization.ipnyb 内の運用化 Jupyter Notebook が、格納されたモデルを使用して、Azure によってホストされている Web サービスでモデルを呼び出すための関数とスキーマをビルドします。 ノートブックは、関数をテストし、資産を LSTM_o16n.zip ファイルに圧縮します。 ファイルは、デプロイ用の Azure ストレージ コンテナー上に読み込まれます。

LSTM_o16n.zip デプロイ ファイルには、次のアーティファクトが含まれます。

- **webservices_conda.yaml**: デプロイ ターゲットで LSTM モデルを実行するために必要な Python パッケージを定義します。  
- **service_schema.json**: LSTM モデルによって予期されるデータ スキーマを定義します。   
- **lstmscore.py**: デプロイ ターゲットが新しいデータを記録するために実行されている関数を定義します。    
- **modellstm.json**: LSTM アーキテクチャを定義します。 lstmscore.py 関数では、モデルを初期化するためにアーキテクチャと重みを読み取ります。
- **modellstm.h5**: モデルの重みを定義します。
- **test_service.py**: テスト データ レコードでデプロイ エンドポイントを呼び出すテスト スクリプトです。 
- **PM_test_files.pkl**: test_service.py スクリプトは、PM_test_files.pkl ファイルからエンジン データの履歴を読み取り、LSTM で必要なサイクルを Web サービスに送信し、エンジンの障害の可能性を返します。

ノートブックは、デプロイ用の運用資産をパッケージ化する前に、モデル定義を使用して関数をテストします。 Web サービスを設定してテストする手順は、Code/3_operationalization.ipnyb Notebook の最後に含まれています。

## <a name="conclusion"></a>まとめ

このチュートリアルでは、予測にセンサーの値を使用する単純なシナリオを使用しています。 高度な予測メンテナンスのシナリオ ([予測メンテナンスのモデリング ガイド R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)など) では、メンテナンス レコードの履歴、エラー ログ、マシンの機能など、多くのデータ ソースを使用できます。 別のデータ ソースをディープ ラーニングで使用するには、異なる処理が必要な場合があります。


## <a name="references"></a>参照

次の参照資料では、各種のプラットフォームを対象とした、その他の予測メンテナンス ユース ケースの例が示されています。

* [予測メンテナンス ソリューション テンプレート](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [予測メンテナンスのモデリング ガイド](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R サービスを使用した予測メンテナンス モデリング ガイド](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [予測メンテナンス モデリング ガイド Python ノートブック](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark を使用する予測メンテナンス](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [現実的なシナリオに対応した予測メンテナンス](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>次の手順

これ以外にも、Machine Learning Workbench 内で利用できる、製品の他の機能を示す多くのサンプル シナリオがあります。 
