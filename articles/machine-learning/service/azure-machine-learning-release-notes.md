---
title: このリリースの新機能
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service と Machine Learning および Data Prep Python SDK の最新情報について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742764"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning service のリリース ノート

この記事では、Azure Machine Learning service の各リリースについて説明します。 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6

+ **SDK リファレンス ドキュメント**: https://aka.ms/aml-sdk

+ **バグの修正**:このリリースには主に、軽微なバグの修正が含まれます

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **SDK リファレンス ドキュメント**: https://aka.ms/data-prep-sdk

+ **新機能**
  + `to_bool` 関数では、一致しない値を Error 値に変換できるようになりました。 これは、`to_bool` および `set_column_types` の新しい既定の不一致動作です。一方、以前の既定の動作では一致しない値を False に変換していました。
  + `to_pandas_dataframe` を呼び出すときに、数値列の null 値/欠落値を NaN と解釈する新しいオプションがあります。
  + 一部の式の戻り値の型をチェックして、型の一貫性と失敗を早期に確認する機能が追加されました。
  + `parse_json` を呼び出して、列内の値を JSON オブジェクトとして解析し、それらを複数の列に拡張できるようになりました。

+ **バグの修正**
  + Python 3.5.2 で `set_column_types` をクラッシュしていたバグを修正しました。
  + AML イメージを使用してデータストアに接続するときにクラッシュしていたバグを修正しました。

+ **更新プログラム**
  * 使用開始チュートリアル、ケース スタディ、および攻略ガイドの[サンプルの Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04: 一般公開

Azure Machine Learning service の一般提供が開始されました。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
このリリースでは、[Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute)を通じて、新しいマネージド コンピューティング エクスペリエンスを発表します。 このコンピューティング ターゲットは、Azure Machine Learning の Azure Batch AI コンピューティングの後継となります。 

このコンピューティング ターゲットは、
+ モデル トレーニングとバッチ推論に使用されます
+ シングルノードからマルチノードのコンピューティングです
+ クラスター管理とジョブ スケジューリングをユーザーに代わって実行します
+ 既定で自動スケーリングを実行します
+ CPU と GPU の両方のリソースをサポートします 
+ コストを抑えるために低優先度の VM を使用できるようにします

Azure Machine Learning コンピューティングは、Python、Azure portal、または CLI を使用して作成できます。 お客様のワークスペースのリージョンに作成する必要があり、他のワークスペースにアタッチすることはできません。 このコンピューティング ターゲットでは、お客様の実行に Docker コンテナーを使用しており、お客様の依存関係をパッケージ化することですべてのノードで同じ環境を再現します。

> [!Warning]
> Azure Machine Learning コンピューティングを使用するには、新しいワークスペースを作成することをお勧めします。 ユーザーが Azure Machine Learning コンピューティングを既存のワークスペースで作成しようとしてエラーが発生することはまずありません。 お客様のワークスペースにある既存のコンピューティングは、引き続き支障なく動作します。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大な変更**
  + このリリースでは、Azure Machine Learning で VM の作成のサポートが廃止されます。 引き続き、既存のクラウド VM またはリモートのオンプレミス サーバーをアタッチすることができます。 
  + また、Batch AI のサポートも廃止となります。そのすべては今後、Azure Machine Learning コンピューティングを通じてサポートされます。

+ **[新規]**
  + 機械学習パイプライン:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **更新**
  + 機械学習パイプライン:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) で runconfig が使用可能になりました
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) で SQL データソースとの間のコピーが可能になりました
    + パブリッシュ済みのパイプラインの実行に関するスケジュールを作成および更新できる SDK のスケジュール機能

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **重大な変更** 
  * `SummaryFunction.N` の名前が `SummaryFunction.Count` に変更されました。
  
+ **バグの修正**
  * リモート実行でデータストアに対する読み書きを行う際、最新の AML Run Token を使用します。 以前は、AML Run Token が Python で更新された場合、Data Prep ランタイムが最新の AML Run Token によって更新されませんでした。
  * より明確なエラー メッセージの追加
  * Spark によって `Kryo` シリアル化が使用される際に to_spark_dataframe() がクラッシュすることがなくなりました
  * 値のカウント インスペクターで表示できる一意の値が 1,000 件を超えました
  * ランダム分割が、元の Dataflow に名前がない場合に失敗しなくなりました  

+ **詳細情報**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>ドキュメントとノートブック
+ ML パイプライン
  + パイプラインの概要、バッチ スコーピング、スタイル転送の例についての新しいノートブックと更新されたノートブック: https://aka.ms/aml-pipeline-notebooks
  + [最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法について
  + [パイプラインを使用してバッチ予測を実行](how-to-run-batch-predictions.md)する方法について
+ Azure Machine Learning コンピューティング ターゲット
  + この新しいマネージド コンピューティングを使用するように [サンプル ノートブック]https://aka.ms/aml-notebooks) が更新されました。
  + [このコンピューティングについて](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: 新機能
+ ポータルで [Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute) タイプを作成して管理します。
+ Azure Machine Learning コンピューティングのクォータ使用率を監視し、[クォータを要求](how-to-manage-quotas.md)します。
+ Azure Machine Learning コンピューティング クラスターの状態をリアルタイムで表示します。
+ Azure Machine Learning コンピューティングと Azure Kubernetes Service の作成用に仮想ネットワークのサポートが追加されました。
+ 既存のパラメーターを使用してお客様のパブリッシュ済みのパイプラインを再実行します。
+ 分類モデル (リフト、ゲイン、キャリブレーション、モデルの説明可能性を備えた特徴重要度グラフ) と回帰モデル (残差、およびモデルの説明可能性を備えた特徴重要度グラフ) のための新しい[自動化された機械学習グラフ](how-to-track-experiments.md#auto)。 
+ パイプラインを Azure portal で表示できます




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大な変更** 
  * *azureml.train.widgets* 名前空間は、*azureml.widgets* に移動されました。
  * *azureml.core.compute.AmlCompute* では、*azureml.core.compute.BatchAICompute* クラスと *azureml.core.compute.DSVMCompute* クラスが非推奨になりました。 前者のクラスは、今後のリリースで削除されます。 現在、AmlCompute クラスには以前の定義があり、単に vm_size と max_nodes が必要で、ジョブが送信されたときにご利用のクラスターは 0 から max_nodes に自動的にスケーリングされます。 [サンプル ノートブック]https://github.com/Azure/MachineLearningNotebooks/tree/master/training) は、この情報で更新されており、使用方法の例が提供されます。 この単純化と今後のリリースで登場するより魅力あふれる多くの機能にご満足いただければ幸いです。

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

Data Prep SDK の詳細については、[リファレンス ドキュメント](https://aka.ms/data-prep-sdk)を参照してください。
+ **新機能**
   * DataPrep パッケージを実行して、データセットまたはデータフローに対するデータ プロファイルを表示するために、新しい DataPrep CLI が作成されました
   * 使いやすさを向上させるために SetColumnType API が再設計されました
   * smart_read_file から auto_read_file に名前が変更されました
   * データ プロファイルに傾斜と尖度が含まれるようになりました
   * 層化抽出法でサンプリングすることができます
   * CSV ファイルを含む zip ファイルからの読み取りが可能になりました
   * ランダム分割でデータセットを行型に分割できます (例: test-train セットに分割)
   * `.dtypes` を呼び出すことでデータフローまたはデータ プロファイルから列のデータ型をすべて取得できます
   * `.row_count` を呼び出すことでデータフローまたはデータ プロファイルから行数を取得できます

+ **バグの修正**
   * 長整数型から倍精度浮動小数点型の変換が修正されました 
   * 列の追加後のアサートが修正されました 
   * 場合によってはグループが検出されない FuzzyGrouping での問題が修正されました
   * 複数列の並べ替え順を優先する並べ替え関数が修正されました
   * `pandas` で処理される方法と同様になるように AND/OR 式が修正されました
   * dbfs パスからの読み取りが修正されました
   * エラー メッセージがわかりやすくなりました 
   * AML トークンを使用してリモート コンピューティング先で読み取るときに、失敗しないようになりました
   * Linux DSVM 上で失敗しないようになりました
   * 文字列以外の値が文字列の述語にある場合にクラッシュしなくなりました
   * Dataflow が正常に失敗する必要があるときに、アサーション エラーが処理されるようになりました
   * Azure Databricks 上に保存場所をマウントした dbutils がサポートされるようになりました

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure ポータル 
Azure Machine Learning サービスの Azure portal では、次の更新が加えられました。
  * パブリッシュされたパイプライン用の新しい **[パイプライン]** タブ。
  * 既存の HDInsight クラスターをコンピューティング ターゲットとしてアタッチする操作が新たにサポートされました。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大な変更** 
  * * Workspace.compute_targets、datastores、experiments、images、models、および *webservices* が、メソッドではなく、プロパティになりました。 たとえば、*Workspace.compute_targets()* は、*Workspace.compute_targets* に置き換えます。
  * *Run.get_submitted_run* が廃止され、*Run.get_context* に置き換えられました。 前者のメソッドは、今後のリリースで削除されます。
  * *PipelineData* クラスは、データストア オブジェクトを datastore_name ではなく、パラメーターとして受け付けるようになりました。 同様に、*パイプライン*は default_datastore_name ではなく default_datastore を受け入れます。

+ **新機能**
  * Azure Machine Learning Pipelines の[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb)で、MPI の手順が使われるようになりました。
  * Jupyter ノートブック用の RunDetails ウィジェットが更新され、パイプラインの視覚化が表示されるようになりました。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **新機能**
  * データ プロファイルにタイプ カウントが追加されました 
  * 値のカウントとヒストグラムが使用可能になりました
  * データ プロファイル内のパーセンタイルが追加されました
  * 集計で中央値が使用可能になりました
  * Python 3.7 が新たにサポートされました
  * データストアを含んだデータ フローを DataPrep パッケージに保存すると、データ ストア情報が DataPrep パッケージの一部としてを永続化されます
  * データ ストアへの書き込みが新たにサポートされました 
        
+ **バグ修正**
  * 64 ビット符号なし整数のオーバーフローが Linux で正しく処理されるようになりました
  * smart_read でのプレーン テキスト ファイルの不適切なテキスト ラベルが修正されました
  * 文字列の列タイプがメトリック ビューで表示されるようになりました
  * タイプ カウントが修正され、各種ではなく、1 つの FieldType にマップされた ValueKinds が表示されるようになりました
  * Write_to_csv で、パスが文字列として指定されている場合に処理が失敗しないようになりました
  * Replace を使用する際に、"find" を空白のままにしても処理が失敗しないようになりました 

## <a name="2018-10-12"></a>2018 年 10 月 12 日

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新機能**
  * 新しいワークスペースの作成時、マルチ テナントがサポートされます。

+ **修正済みのバグ**
  * Web サービスをデプロイするときに、pynacl ライブラリのバージョンをピン留めする必要がなくなりました。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **新機能**
  * メソッド transform_partition_with_file(script_path) が追加されました。このメソッドにより、実行する Python ファイルのパスを渡すことができます。

## <a name="2018-10-01"></a>2018 年 10 月 1 日

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[バージョン 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) には、いくつかの新機能、追加のドキュメント、バグ修正、および追加の[サンプル ノートブック](https://aka.ms/aml-notebooks)が同梱されています。

バグおよび対処法については、[既知の問題のリスト](resource-known-issues.md)を参照してください。

+ **重大な変更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 返却辞書、以前返却されたリスト。 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API ドキュメントを参照してください。

  * 自動化された機械学習で、正規化平均二乗誤差がプライマリ メトリックから削除されました。

+ **HyperDrive**
  * ベイジアンに関する HyperDrive のさまざまなバグが修正されました。また、メトリックを取得する呼び出しのパフォーマンスが改善されました。 
  * Tensorflow が 1.9 から 1.10 にアップグレードされました。 
  * Docker イメージがコールド スタート用に最適化されました。 
  * 終了時に 0 以外のエラー コードが発生した場合でも、ジョブの状態が正確に報告されます。 
  * SDK で RunConfig 属性が検証されます。 
  * HyperDrive 実行オブジェクトで、通常の実行と同様のキャンセルがサポートされます。パラメーターの受け渡しは不要です。 
  * 分散実行および HyperDrive 実行でドロップダウンの値の状態が維持されるようにウィジェットが改善されました。 
  * TensorBoard およびその他のログ ファイルのサポートが、パラメーター サーバー用に修正されました。 
  * サービス側で Intel(R) MPI がサポートされます。 
  * BatchAI での検証時における、分散実行修正用のパラメーター調整のバグが修正されました。 
  * コンテキスト マネージャーがプライマリ インスタンスを識別するようになりました。 

+ **Azure portal での操作**
  * [Run details]\(実行の詳細\) で log_table() と log_row() がサポートされます。 
  * 1、2、または 3 個の数値列、および 1 個のカテゴリ列 (オプション) を含むテーブルおよび行のグラフが自動的に作成されます。

+ **自動化された機械学習**
  * エラー処理とドキュメントが改善されました。 
  * 実行プロパティ取得のパフォーマンスの問題が修正されました。 
  * 実行の継続に関する問題が修正されました。 
  * 繰り返しのアンサンブルに関する問題が修正されました。
  * MAC OS 上でトレーニングがハングするバグが修正されました。
  * カスタム検証シナリオで、マクロ平均 PR/ROC 曲線がダウンサンプリングされます。
  * 余分なインデックス ロジックが削除されました。
  * get_output API からフィルターが削除されました。

+ **パイプライン**
  * パイプラインを直接バブリッシュするメソッド Pipeline.publish() が追加され、最初の実行が不要になりました。   
  * パブリッシュ済みのパイプラインから生成されたパイプライン実行をフェッチするメソッド PipelineRun.get_pipeline_runs() が追加されました。

+ **Project Brainwave**
  * FPGA 上で使用可能な新しい AI モデルのサポートが更新されました。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[バージョン 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) には、以下の機能とバグ修正が組み込まれています。

+ **新機能**
  * ワンホット エンコードがサポートされます。
  * 分位変換がサポートされます。
   
+ **バグ修正:**
  * どのバージョンの Tornado でも機能します。Tornado のバージョンをダウングレードする必要はありません。
  * 上位 3 つの値だけでなく、すべての値のカウントが評価されます。

## <a name="2018-09-public-preview-refresh"></a>2018 年 9 月 (パブリック プレビューを更新)

Azure Machine Learning が更新されてリリースされました。このリリースの詳細については、 https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/ を参照してください。

## <a name="older-notes-sept-2017---jun-2018"></a>以前のリリース ノート: 2017 年 9 月から 2018 年 6 月
### <a name="2018-05-sprint-5"></a>2018-05 (スプリント 5)

このリリースの Azure Machine Learning では、次のことができます。
+ 量子化バージョンの ResNet 50 で画像の特徴を生成し、それらの特徴に基づいて分類子をトレーニングし、待機時間の極めて短い推論のために[そのモデルを Azure 上の FPGA にデプロイします](../service/how-to-deploy-fpga-web-service.md)。

+ [カスタムの Azure Machine Learning パッケージ](../desktop-workbench/reference-python-package-overview.md)を使用して、精度の高い機械学習および深層学習モデルをすばやく構築し、デプロイします

### <a name="2018-03-sprint-4"></a>2018-03 (スプリント 4)
**バージョン番号**: 0.1.1801.24353  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 引き続きフィードバックをお寄せください。

**注目すべき新機能と変更**

- リモート Docker ベースでの実行に加えて、リモート Ubuntu VM 上にあるスクリプトを独自の環境でネイティブに実行できるようになりました。
- CLI ベースのエクスペリエンスに加えて用意された Workbench アプリ内での新しい環境エクスペリエンスにより、コンピューティング ターゲットを作成し、構成を実行できます。
![環境タブ](media/azure-machine-learning-release-notes/environment-page.png)
- カスタマイズ可能な実行履歴レポート ![新しい実行履歴レポートの画像](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**詳細な更新**

以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

#### <a name="workbench-ui"></a>Workbench UI
- カスタマイズ可能な実行履歴レポート
  - 実行履歴レポート用のグラフ構成の改良
    - 使用されたエントリポイントを変更可能
    - 最上位レベルのフィルターを追加および変更可能 ![フィルターの追加](media/azure-machine-learning-release-notes/add-filters.jpg)
    - グラフと統計情報を追加および変更可能 (ドラッグ アンド ドロップで再配置)
    ![新しいグラフの作成](media/azure-machine-learning-release-notes/configure-charts.png)

  - 実行履歴レポートの CRUD
  - 既存の実行履歴リスト ビューの構成ファイルをすべてサーバー側のレポートに移動しました。これは、選択したエントリ ポイントからの実行でパイプラインのように機能します。

- 環境タブ
  - 新しいコンピューティング ターゲットを簡単に追加し、構成ファイルをプロジェクトに対して実行 ![新しいコンピューティング ターゲット](media/azure-machine-learning-release-notes/add-new-environments.png)
  - 単純なフォームベースの UX を使用して、構成ファイルを管理、更新
  - 実行環境を準備するための新しいボタン

- サイド バー内のファイルの一覧のパフォーマンスの向上

#### <a name="data-preparation"></a>データの準備 
- Azure Machine Learning Workbench を使用して、既知の列名で列を検索できるようになりました。


#### <a name="experimentation"></a>実験
- Azure Machine Learning Workbench で、独自の Python または PySpark 環境でのネイティブなスクリプト実行がサポートされました。 この機能によって、ユーザーは独自の環境をリモート VM 上で作成、管理できるようになります。また、Azure Machine Learning Workbench を使用してターゲットに対してスクリプトを実行できます。 「[Azure Machine Learning 実験サービスの構成](../desktop-workbench/experimentation-service-configuration.md)」を参照してください 

#### <a name="model-management"></a>モデル管理
- デプロイされたコンテナーのカスタマイズのサポート: apt-get などを使用して外部ライブラリのインストールを許可することで、コンテナー イメージをカスタマイズできます。pip によるインストールが可能なライブラリに限らず実施できるようになりました。 詳細については、この[ドキュメント](../desktop-workbench/model-management-custom-container.md)をご覧ください。
  - マニフェスト、画像、サービスの作成コマンドで `--docker-file myDockerStepsFilename` フラグとファイル名を使用します。
  - 基本イメージが Ubuntu であり、変更できない場合があります。
  - コマンドの例: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01 (スプリント 3) 
**バージョン番号**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このスプリントでの更新と機能強化は次のとおりです。 これらの更新の多くは、ユーザー フィードバックの直接の結果として行われました。 

### <a name="2017-12-sprint-2"></a>2017-12 (スプリント 2)
**バージョン番号**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このリリースは、Azure Machine Learning の 3 回目の更新です。 この更新には、Workbench アプリ、コマンド ライン インターフェイス (CLI)、およびバックエンド サービスの改良が含まれます。 当社に長所と欠点を送信していただき誠にありがとうございます。 以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 

### <a name="2017-11-sprint-1"></a>2017-11 (スプリント 1) 
**バージョン番号**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このリリースで当社は、Workbench アプリ、CLI、およびバックエンド サービス レイヤーにおけるセキュリティ、安定性、保守容易性に関して機能強化を行っています。 

### <a name="2017-10-sprint-0"></a>2017-10 (スプリント 0) 
**バージョン番号**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このリリースは、Microsoft Ignite 2017 カンファレンスでの最初のパブリック プレビューに続く、Azure Machine Learning Workbench の初回の更新です。 今回のリリースでの主要な更新プログラムは、信頼性と安定化の修正です。 

## <a name="next-steps"></a>次の手順

[Azure Machine Learning](../service/overview-what-is-azure-ml.md) の概要をご覧ください。
