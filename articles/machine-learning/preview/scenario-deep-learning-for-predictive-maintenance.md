---
title: "予測メンテナンスのためのディープ ラーニングの実際のシナリオ | Microsoft Docs"
description: "予測メンテナンスのためのディープ ラーニングに関するチュートリアルを Azure Machine Learning Workbench で複製する方法を説明します。"
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>予測メンテナンスのためのディープ ラーニングの実際のシナリオ

ディープ ラーニングは、機械学習における最も一般的なトレンドの 1 つです。 ディープ ラーニングは、無人自動車、音声および画像認識、ロボット工学、金融など、多くの分野やアプリケーションで使用されています。 ディープ ラーニングは、脳の形状 (生物学的ニューラル ネットワーク) と機会学習によってインスパイアされた一連のアルゴリズムです。 認知科学者は、通常、ディープ ラーニングを人工ニューラル ネットワーク (ANN) と呼びます。

予測メンテナンスも、一般的に行われています。 予測メンテナンスでは、装置の状態を判断し、メンテナンスの実行時期を予測するために役立つ多数の技法が設計されています。 予測メンテナンスの一般的な用途には、障害予測、障害診断 (根本原因分析)、障害検出、障害の種類の分類、障害発生後の推奨される軽減策やメンテナンス アクションなどがあります。

予測メンテナンス シナリオでは、装置の状態を監視するために、時間をかけてデータが収集されます。 目標は、障害を予測し、最終的には障害の発生を防ぐために役立つパターンを見つけることです。 [長期短期記憶 (LSTM: Long Short Term Memory)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) ネットワークの使用は、予測メンテナンスで特に魅力的なディープ ラーニング手法です。 LSTM ネットワークは、シーケンスから学習することに適しています。 時系列データを使用して、長期間を振り返ってエラー パターンを検出できます。

このチュートリアルでは、[予測メンテナンス](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)に関する記事で説明されているデータ セットとシナリオ向けの LSTM ネットワークを構築します。 このネットワークを使用して、航空エンジンの残存耐用年数を予測します。 テンプレートでは、シミュレートされた航空機のセンサー値を使用して、航空エンジンの障害がいつ発生するかを予測します。 この予測を使用して、メンテナンスを事前に計画して障害を防ぐことができます。

このチュートリアルでは、[Keras](https://keras.io/) ディープ ラーニング ライブラリを使用し、バックエンドとして Microsoft Cognitive Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) を使用します。

このチュートリアルのサンプルがあるパブリック GitHub リポジトリは、[https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) です。

## <a name="use-case-overview"></a>ユース ケースの概要

このチュートリアルでは、シミュレートされた航空エンジンの run-to-failure イベントの例を使用して、予測メンテナンス モデリング プロセスを示します。 

ここで説明するモデリング データの暗黙的な前提は、資産に進行する劣化パターンがあることです。 このパターンは、資産のセンサーの測定値に反映されます。 資産のセンサー値を時間をかけて調査することで、機械学習アルゴリズムは、センサー値、センサー値の変化、障害履歴の間にある関係を学習できます。 この関係を使用して、将来の障害が予測されます。 

サンプル データを独自のデータに置き換える前に、データ形式を確認し、テンプレートの 3 つの手順をすべて完了しておくことをお勧めします。

## <a name="prerequisites"></a>前提条件

- [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版も使用できます)。
- ワークスペース作成済みの Azure Machine Learning Workbench。
- モデルを運用化するために: ローカル デプロイ環境がセットアップされた Azure Machine Learning Operationalization と [Azure Machine Learning モデル管理アカウント](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)。

## <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。

1. Machine Learning Workbench を開きます。
2. **[プロジェクト]** ページで **[+]** をクリックし、**[新しいプロジェクト]** を選択します。
3. **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4. **[プロジェクト テンプレートの検索]** 検索ボックスに「**予測メンテナンス**」と入力し、テンプレートを選択します。
5. **[作成]**を選択します。

## <a name="prepare-the-notebook-server-computation-target"></a>ノートブック サーバーの計算ターゲットの準備

ローカル コンピューターで、Machine Learning Workbench の **[ファイル]** メニューから **[コマンド プロンプトを開く]** または **[PowerShell を開く]** のいずれかを選択します。 選択したオプションのコマンド プロンプト ウィンドウで、次のコマンドを実行します。

`az ml experiment prepare --target docker --run-configuration docker`

ノートブック サーバーは、DS4_V2 standard [Data Science Virtual Machine (DSVM) for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) で実行することをお勧めします。 DSVM を構成したら、次のコマンドを実行して Docker イメージを準備します。

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Docker イメージが準備できたら、Jupyter Notebook サーバーを開きます。 Jupyter Notebook サーバーを開くには、Machine Learning Workbench の **[ノートブック]** タブに移動するか、`az ml notebook start` でブラウザー ベースのサーバーを開始します。

ノートブックは、Jupyter 環境内の Code ディレクトリに格納されています。 これらのノートブックを、Code\1_data_ingestion_and_preparation.ipynb から始めて、番号順に実行します。

[project_name]_Template [connection_name] と一致するカーネルを選択し、**[Set Kernel]\(カーネルの設定\)** を選択します。

## <a name="data-description"></a>データの説明

テンプレートでは、PM_train.txt、PM_test.txt、および PM_truth.txt というファイル内の 3 つのデータセットを入力として使用します。

-  **トレーニング データ**: 航空エンジンの run-to-failure データ。 トレーニング データ (PM_train.txt) は、複数の多変量の時系列で構成され、時間単位として "*サイクル*" が使用されています。 これには、サイクルごとに 21 個のセンサーの読み取り値が含まれます。 

    各時系列は、同じ型の異なるエンジンから生成されるとみなすことができます。 各エンジンは、程度が異なる初期摩耗と、製造時のばらつきを持って開始されるものとします。 ユーザーはこの情報を知りません。 

    このシミュレートされたデータでは、エンジンは、各時系列の開始時には正常に動作するものとします。 それは、一連の運転サイクル中のある時点で劣化し始めます。 劣化は進行して規模が大きくなっていきます。 

    定義済みのしきい値に達すると、エンジンはこれ以上運転するのは安全でないとみなされます。 各時系列の最後のサイクルは、対応するエンジンの障害点とみなすことができます。

-   **テスト データ**: 障害イベントの記録がない航空エンジンの運転データ。 テスト データ (PM_test.txt) のデータ スキーマは、トレーニング データと同じです。 唯一の違いは、このデータはいつ障害が発生するかを示さないことです (最後の期間は障害点を*表わしません*)。 障害が発生する前にエンジンが持ちこたえるサイクル数は不明です。

- **実データ**: テスト データ内の各エンジンの実際の残存サイクルの情報です。 基礎となる実データは、テスト データ内のエンジンの残存動作サイクル数を示します。

## <a name="scenario-structure"></a>シナリオの構造

このシナリオの内容は、[GitHub repository] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) から入手できます。 

リポジトリでは、[readme] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) ファイルに、データの準備からモデルのビルドと運用化までのプロセスの概要が示されています。 3 つの Jupyter Notebook は、リポジトリの [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) フォルダーから入手できます。 

次に、シナリオの手順を追ったワークフローについて説明します。

### <a name="task-1-data-ingestion-and-preparation"></a>タスク 1: データの取り込みと準備

Code/1_data_ingestion_and_preparation.ipnyb のデータ取り込み Jupyter Notebook が、3 つの入力データ セットを Pandas データフレーム形式に読み込みます。 次に、モデリング用にデータを準備し、何らかの予備的なデータの視覚化を実行します。 その後、データは PySpark 形式に変換され、次のモデリング タスクで使用するために、Azure サブスクリプションの Azure Blob Storage コンテナーに格納されます。

### <a name="task-2-model-building-and-evaluation"></a>タスク 2: モデルのビルドと評価

Code/2_model_building_and_evaluation.ipnyb 内のモデル ビルド Jupyter Notebook が、PySpark のトレーニング データとテスト データのセットを BLOB ストレージから読み取ります。 次に、トレーニング データ セットを使用して LSTM ネットワークがビルドされます。 テスト セットに対するモデルのパフォーマンスが測定されます。 結果モデルがシリアル化され、運用化タスクで使用するためにローカル コンピューティング コンテキストに格納されます。

### <a name="task-3-operationalization"></a>タスク 3: 運用化

Code/3_operationalization.ipnyb 内の運用化 Jupyter Notebook が、格納されたモデルを使用して、Azure によってホストされている Web サービスでモデルを呼び出すために必要な関数とスキーマをビルドします。 ノートブックは、関数をテストし、運用化資産を .zip ファイルに zip (圧縮) します。

圧縮ファイルには、次のファイルが含まれます。

- **modellstm.json**: デプロイ用のスキーマ定義ファイル。 
- **lstmscore.py**: Azure Web サービスによって要求される **init()** 関数と **run()** 関数。
- **lstm.model**: モデル定義ディレクトリ。

ノートブックは、デプロイ用の運用資産をパッケージ化する前に、モデル定義を使用して関数をテストします。 デプロイの手順は、ノートブックの末尾に記載されています。


## <a name="conclusion"></a>まとめ

このチュートリアルでは、1 つのデータ ソース (センサー値) のみで予測を行う単純なシナリオを使用しています。 [予測メンテナンス モデリング ガイド R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) などの高度な予測メンテナンス シナリオでは、多くのデータ ソースが使用される場合があります。 他のデータ ソースには、メンテナンスの履歴レコード、エラー ログ、コンピューターとオペレーターの特徴などがあります。 別のデータ ソースをディープ ラーニング ネットワークで使用するには、異なる種類の処理が必要な場合があります。 モデルを適切なパラメーター (ウィンドウ サイズなど) でチューニングすることも重要です。 

このシナリオの関連する部分を編集し、別の問題のシナリオを試すことができます。たとえば、「[Predictive Maintenance Modelling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)」(予測メンテナンス ガイド) で説明されている複数の他のデータ ソースが関係しているシナリオがあります。


## <a name="references"></a>参照

- [予測メンテナンス ソリューション テンプレート](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [予測メンテナンスのモデリング ガイド](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [予測メンテナンス モデリング ガイド Python ノートブック](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [PySpark を使用する予測メンテナンス](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

