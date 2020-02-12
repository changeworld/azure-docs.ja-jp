---
title: ML パイプラインとは
titleSuffix: Azure Machine Learning
description: この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習 (ML) パイプラインの利点について学習します。 機械学習パイプラインは、データ サイエンティストが機械学習ワークフローを構築、最適化、管理するために使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 840c5cf061658f3210fec963b82b490185b92a4b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905717"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning パイプラインとは

Azure Machine Learning パイプラインを使用すると、機械学習プロジェクトにワークフローを作成できます。 これらのワークフローには、次のような多くの利点があります。 

+ 簡便性
+ 速度
+ 再現性
+ 柔軟性
+ バージョン管理と追跡
+ モジュール性 
+ 品質保証
+ コスト管理

これらの利点は、機械学習プロジェクトが純粋な探索を超えてイテレーションに移行するとすぐに重要になります。 単純なワンステップ パイプラインでも役に立つことがあります。 機械学習プロジェクトは、多くの場合、複雑な状態にあり、1 つのワークフローを正確に完成させることが簡単なプロセスにするための救済方法になることがあります。

[最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法を学習します。

![Azure Machine Learning での機械学習パイプライン](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>どの Azure パイプライン テクノロジを使用すべきか

Azure クラウドでは、目的が異なるさまざまなパイプラインが提供されています。 次の表に、それらのさまざまなパイプラインとその用途を示します。

| パイプライン | 実行内容 | Canonical パイプ |
| ---- | ---- | ---- |
| Azure Machine Learning パイプライン | 機械学習シナリオ用のテンプレートとして使用できる、再利用可能な機械学習ワークフローを定義します。 | データ -> モデル |
| [Azure Data Factory のパイプライン](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 1 つのタスクを実行するために必要なデータ移動、変換、および制御アクティビティをグループ化します。  | データ -> データ |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | 任意のプラットフォーム/任意のクラウドへの自分のアプリケーションの継続的インテグレーションとデリバリー  | コード -> アプリ/サービス |

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML パイプラインでできること

Azure Machine Learning パイプラインは、独立して実行できる完全な機械学習タスクのワークフローです。 サブタスクは、パイプライン内の一連のステップとしてカプセル化されます。 Azure Machine Learning パイプラインは、Python スクリプトを呼び出すのと同じくらいシンプルなものにすることができるため、ほとんどのことを実行 "_できます_"。 パイプラインは、次のような機械学習タスクに重点を置く "_必要があります_"。

+ インポート、検証、クリーンアップ、マンジングと変換、正規化、ステージングなどのデータ準備
+ 引数のパラメーター化、ファイルパス、ログ/レポートの構成などのトレーニング構成
+ 特定のデータ サブセットの指定、さまざまなハードウェアのコンピューティング リソース、分散処理、進行状況の監視など、効率的かつ繰り返しのトレーニングと検証
+ バージョン管理、スケーリング、プロビジョニング、アクセス制御などのデプロイ 

個別のステップにより、複数のデータ サイエンティストが、コンピューティング リソースを過剰に使用することなく、同じパイプラインで同時に作業を行うことができます。 また、個別のステップにより、ステップごとに異なるコンピューティングの種類/サイズを使用することが容易になります。

パイプラインが設計された後には、多くの場合、パイプラインのトレーニング ループを中心にさらに多くの微調整が行われます。 パイプラインを再実行すると、更新されたトレーニング スクリプトなど、再実行する必要があるステップにジャンプして実行されます。 再実行する必要がないステップはスキップされます。 ステップの遂行に使用される変更されていないスクリプトにも同じ分析が適用されます。 この再利用機能により、基のデータが変更されていない場合に、データ インジェストや変換など、コストや時間のかかる処理を回避できます。

Azure Machine Learning では、パイプライン内の各ステップに対して、PyTorch や TensorFlow などの各種のツールキットとフレームワークを使用できます。 Azure により、使用するさまざまな[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md)の調整が行われるため、中間データをダウンストリームのコンピューティング ターゲットと共有できます。

Azure portal または[ワークスペースのランディング ページ (プレビュー)](https://ml.azure.com) で直接、[パイプライン実験のメトリックを追跡](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)できます。 パイプラインを発行した後は、任意のプラットフォームまたはスタックからパイプラインを再実行できる REST エンドポイントを構成できます。

つまり、パイプラインを使用することで、機械学習のライフサイクルの複雑なタスクをすべて支援できます。 その他の Azure パイプライン テクノロジには、データを操作するための [Azure Data Factory パイプライン](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities)や、継続的な統合とデプロイのための [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) などの独自の強味があります。 ただし、機械学習に重点を置いている場合は、Azure Machine Learning パイプラインがワークフローのニーズに最適な選択肢となる可能性があります。 

## <a name="what-are-azure-ml-pipelines"></a>Azure ML パイプラインとは

Azure ML パイプラインでは、順序付けられた一連のステップで完全な論理ワークフローが実行されます。 各ステップは、個別の処理アクションです。 パイプラインは、Azure Machine Learning [実験](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)のコンテキストで実行されます。

ML プロジェクトの初期段階では、Azure ワークスペースとリソース構成のすべての作業、データ準備、実行構成、トレーニング、検証を実行する、1 つの Jupyter Notebook または Python スクリプトで十分です。 しかし、関数とクラスが 1 つの命令型のコード ブロックにとってより適したものにすぐになるように、ML ワークフローは、モノリシック ノートブックやスクリプトにとってより適したものにすぐになります。 

パイプラインでは、ML タスクをモジュール化することで、コンポーネントが "1 つのこと (だけ) をうまく実行する" 必要があるコンピューター サイエンスの命令型をサポートします。 モジュール性は、チームでプログラミングする際にプロジェクトの成功に不可欠ですが、単独で作業している場合でも、小さな ML プロジェクトが個別のタスクに関与する場合でも、それぞれにかなりの複雑さがあります。 タスクには、ワークスペースの構成とデータ アクセス、データの準備、モデルの定義と構成、デプロイなどがあります。 1 つ以上のタスクの出力が別のタスクへの入力となりますが、あるタスクの正確な実装の詳細でも、せいぜい、次のタスクにとって関連性のない、気をそらすものになるに過ぎません。 最悪の場合、1 つのタスクの計算状態が別のタスクのバグを引き起こす可能性があります。 

### <a name="analyzing-dependencies"></a>依存関係の分析

多くのプログラミング エコシステムは、リソース、ライブラリ、またはコンパイルの依存関係を調整するツールを備えています。 一般に、これらのツールでは、ファイルのタイムスタンプを使用して依存関係を計算します。 ファイルが変更されると、ファイルとその依存のみが更新 (ダウンロード、再コンパイル、またはパッケージ化) されます。 Azure ML パイプラインでは、この概念が大幅に拡張されています。 パイプラインでは、従来のビルド ツールと同様に、ステップ間の依存関係が計算され、必要な再計算のみが実行されます。 

ただし、Azure ML パイプラインでの依存関係の分析は、単純なタイムスタンプよりも洗練されています。 すべてのステップを別のハードウェアやソフトウェア環境で実行できます。 データの準備は時間のかかるプロセスですが、強力な GPU を搭載したハードウェア上で実行する必要はありません。特定のステップでは、OS 固有のソフトウェアが必要な場合があります。分散トレーニングを使用する必要がある場合があります。 リソースの最適化にかかるコストの削減は重要かもしれませんが、ハードウェアとソフトウェアのリソースのさまざまなバリエーションをすべて手動で処理するのは困難な場合があります。 ステップ間で転送するデータをまったくミスなくすべてを行うことは、さらに困難です。 

パイプラインはこの問題を解決します。 Azure Machine Learning により、パイプラインのステップ間のすべての依存関係が自動的に調整されます。 このオーケストレーションには、Docker イメージのスピンアップとスピンダウン、コンピューティング リソースのアタッチとデタッチ、一貫した自動的な方法でステップ間のデータ移動などが含まれます。

### <a name="reusing-results"></a>結果の再利用

また、ステップの出力を再利用することもできます (選択した場合)。 再利用を可能性として指定し、再計算をトリガーするアップストリームの依存関係がない場合、パイプライン サービスではステップの結果のキャッシュされたバージョンが使用されます。 このような再利用によって、開発時間が大幅に短縮されます。 複雑なデータ準備タスクがある場合は、そのタスクを必要以上に頻繁に再実行している可能性があります。 パイプラインを使用すると、その心配をしないで済みます。必要な場合にはステップが実行され、そうでない場合には実行されません。

この依存関係の分析、オーケストレーション、およびアクティブ化はすべて、[Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) オブジェクトをインスタンス化して `Experiment` に渡し、`submit()` を呼び出すときに、Azure Machine Learning によって処理されます。 

### <a name="coordinating-the-steps-involved"></a>必要なステップの調整

`Pipeline` オブジェクトを作成して実行すると、次のような高レベルのステップが実行されます。

+ 各ステップでは、次に対する要件がサービスによって計算されます。
    + ハードウェアのコンピューティング リソース
    + OS リソース (Docker イメージ)
    + ソフトウェア リソース (Conda/virtualenv の依存関係)
    + データ入力 
+ サービスによりステップ間の依存関係が決定され、その結果、動的な実行グラフになります
+ 実行グラフの各ノードが実行されると、次のことが起こります。
    + サービスによって、必要なハードウェアおよびソフトウェア環境が構成されます (既存のリソースが再利用されることがあります)
    + ステップが実行され、含まれている `Experiment` オブジェクトにログと監視情報が提供されます
    + ステップが完了すると、その出力は次のステップへの入力として準備されるか、ストレージに書き込まれます
    + 不要になったリソースは、完了してデタッチされます

![パイプラインのステップ](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK を使用したパイプラインの構築

[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) では、パイプラインは `azureml.pipeline.core` モジュールで定義されている Python オブジェクトです。 [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) オブジェクトには、1 つ以上の [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) オブジェクトの順序付けられたシーケンスが含まれています。 `PipelineStep` クラスは抽象であり、実際のステップは [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py)、[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)、[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) などのサブクラスになります。 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) クラスでは、パイプライン間で共有できる、再利用可能な一連のステップが保持されます。 `Pipeline` は `Experiment` の一部として実行されます。

Azure ML パイプラインは、Azure Machine Learning ワークスペースに関連付けられています。パイプライン ステップは、そのワークスペース内で使用可能なコンピューティング ターゲットに関連付けられています。 詳細については、「[Azure portal 内で Azure Machine Learning ワークスペースを作成および管理する](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)」または「[Azure Machine Learning でのコンピューティング先とは](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)」を参照してください。

Azure Machine Learning では、コンピューティング ターゲットは ML フェーズが発生する環境です。 ソフトウェア環境には、リモート VM、Azure Machine Learning コンピューティング、Azure Databricks、Azure Batch などがあります。 また、ハードウェア環境は、GPU のサポート、メモリ、ストレージなどによって大きく異なる場合があります。 ステップごとにコンピューティング ターゲットを指定することができます。これにより、コストをきめ細かく制御できます。 プロジェクトの特定のアクション、データ ボリューム、パフォーマンスのニーズに応じて、より強力な (またはそれほど強力ではない) リソースを使用できます。 

## <a name="building-pipelines-with-the-designer"></a>デザイナーを使用したパイプラインの構築

ビジュアル デザイン サーフェスを好む開発者は、Azure Machine Learning デザイナーを使用してパイプラインを作成できます。 このツールにアクセスするには、ワークスペースのホームページで **[デザイナー]** を選択します。  デザイナーでは、デザイン サーフェスにステップをドラッグ アンド ドロップできます。 迅速に開発するために、さまざまな ML タスクで既存のモジュールを使用できます。既存のモジュールは、データ変換からアルゴリズム選択、さらにはトレーニングやデプロイまで、あらゆるものを対象としています。 または、Python スクリプトで定義されている独自のステップを組み合わせて、完全にカスタムのパイプラインを作成することもできます。

パイプラインを視覚的にデザインすると、ステップの入力と出力が視覚的に表示されます。 データ接続をドラッグ アンド ドロップして、パイプラインのデータフローをすばやく理解し、変更することができます。
 
![Azure Machine Learning デザイナーの例](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>実行グラフについて

パイプライン内のステップには、他のステップへの依存関係がある場合があります。 Azure ML パイプライン サービスでは、これらの依存関係を分析および調整する作業が行われます。 結果の "実行グラフ" のノードは、処理ステップです。 各ステップでは、ハードウェアとソフトウェアの特定の組み合わせの作成や再利用、キャッシュされた結果の再利用などが行われる場合があります。 サービスのオーケストレーションとこの実行グラフの最適化により、ML フェーズが大幅に高速化され、コストが削減されます。 

ステップは個別に実行されるため、ステップ間をフローする入力データと出力データを保持するためのオブジェクトは、外部で定義する必要があります。 これは、[DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、および関連付けられているクラスの役割です。 これらのデータ オブジェクトは、そのストレージ構成をカプセル化する [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) オブジェクトに関連付けられています。 `PipelineStep` 基本クラスは、常に `name` 文字列、`inputs` の一覧、および `outputs` の一覧を使用して作成されます。 通常は、`arguments` の一覧もあり、多くの場合、`resource_inputs` の一覧もあります。 通常、サブクラスには追加の引数も含まれます (たとえば、`PythonScriptStep` には、実行するスクリプトのファイル名とパスが必要です)。 

実行グラフは非循環ですが、パイプラインは定期的なスケジュールで実行できます。また、ファイル システムに状態情報を書き込むことができる Python スクリプトを実行して、複雑なプロファイルを作成することもできます。 特定のステップが並列または非同期で実行されるようにパイプラインを設計すると、Azure Machine Learning により、ファンアウトとファンインの依存関係の分析と調整が透過的に処理されます。 一般に、実行グラフの詳細については考慮する必要はありませんが、これは [Pipeline. graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 属性で使用できます。 


### <a name="a-simple-python-pipeline"></a>単純な Python パイプライン

このスニペットは、基本的な `Pipeline` を作成して実行するために必要なオブジェクトと呼び出しを示しています。

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

スニペットは、共通の Azure Machine Learning オブジェクト、`Workspace`、`Datastore`、[ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)、および `Experiment` で開始されます。 次に、コードによって `input_data` と `output_data` を保持するオブジェクトが作成されます。 配列 `steps` には、1 つの要素 (データ オブジェクトを使用して `compute_target` 上で実行する `PythonScriptStep`) が保持されます。 次に、このコードによって `Pipeline` オブジェクト自体がインスタンス化され、ワークスペースとステップの配列が渡されます。 `experiment.submit(pipeline)` を呼び出すと、Azure ML パイプラインの実行が開始されます。 `wait_for_completion()` を呼び出すと、パイプラインが終了するまでブロックされます。 

パイプラインをデータに接続する方法の詳細については、[データへのアクセス方法](how-to-access-data.md)および[データセットの登録方法](how-to-create-register-datasets.md)に関する記事を参照してください。 

## <a name="best-practices-when-using-pipelines"></a>パイプラインを使用する際のベスト プラクティス

ご覧のように、Azure ML パイプラインの作成は、スクリプトの開始よりも少し複雑です。 パイプラインでは、いくつかの Python オブジェクトを構成して作成する必要があります。 

パイプラインの使用が推奨されるいくつかの状況を次に示します。

* チーム環境で: 開発者がプログラムを個別に処理して進化させることができるように、ML タスクを複数の独立したステップに分割します。 

* デプロイ時またはデプロイの直前: 構成を明確にして、スケジュールされたイベントドリブン操作を使用して、データの変更を把握します。

* ML プロジェクトの初期段階または単独作業で: パイプラインを使用してビルドを自動化します。 新しいアイデアを実装する前に、構成と計算の状態を再作成することが心配になってきた場合は、パイプラインを使用してワークフローを自動化することを検討してください。 

キャッシュされた結果を再利用したり、コンピューティング コストをきめ細かく制御したり、プロセスを分離したりすることに熱中しがちですが、パイプラインにはコストがかかります。 アンチパターンには、次のものがあります。

* 問題を分離するための唯一の手段としてパイプラインを使用する。 Python の組み込み関数、オブジェクト、モジュールは、プログラムの状態の混乱を回避するのに大いに役立ちます。 パイプライン ステップは、関数呼び出しよりもはるかにコストがかかります。

* パイプライン ステップ間の強力な結合。 依存するステップを頻繁にリファクタリングするのに前のステップの出力を変更する必要がある場合、その別のステップでは、現在、利点よりもコストの方が高くなる可能性があります。 ステップが過度に結合されているもう 1 つの手掛かりは、ステップへの引数がデータではなく、処理を制御するフラグであることです。 

* コンピューティング リソースを早期に最適化する。 たとえば、多くの場合、データ準備にはいくつかのステージがあり、「並列プログラミングに `MpiStep` を使用できたが、それほど強力ではないコンピューティング ターゲットでは `PythonScriptStep` を使用できた」と思うことがしばしばあります。 また、長期的に見れば、そのようなきめ細かいステップを作成することは、特に、常に再計算するのではなく、キャッシュされた結果を使用する可能性がある場合には、価値があると実証される可能性があります。 ただし、パイプラインは `multiprocessing` モジュールの代わりに使用されることを意図していません。 

プロジェクトが大規模またはデプロイの直前になるまで、パイプラインの粒度はきめ細かくするよりも粗くする必要があります。 ML プロジェクトを "_ステージ_" が含まれているもの、また、パイプラインを特定のステージを移動するための完全なワークフローを提供するものとして考えている場合は、それで合っています。 

## <a name="key-advantages"></a>主な利点

機械学習ワークフローのパイプラインを使用する主な利点を次に示します。

|主な利点|説明|
|:-------:|-----------|
|**無人&nbsp;実行**|複数のステップを無人で確実に並列実行または順次実行するようにスケジュールします。 データの準備とモデリングには数日または数週間かかる可能性があります。パイプラインを使用すると、このプロセスの実行中に他のタスクに専念できます。 |
|**異種コンピューティング**|スケーラブルな異種コンピューティング リソースとストレージの場所全体で確実に調整された複数のパイプラインを使用します。 パイプラインの個々のステップをさまざまなコンピューティング ターゲット上 (HDInsight、GPU Data Science VM、Databricks など) で実行することによって、利用可能なコンピューティング リソースを効率的に使用できます。|
|**再利用**|再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにパイプライン テンプレートを作成します。 単純な REST 呼び出しを使用して外部システムから発行されたパイプラインをトリガーします。|
|**追跡とバージョン管理**|反復処理しながらデータや結果パスを手動で追跡するのではなく、パイプライン SDK を使用して、データ ソース、入力、および出力に明示的に名前を付けてバージョン管理します。 また、生産性を向上させるために、スクリプトとデータを個別に管理することもできます。|
| **モジュール性** | 懸念事項の範囲を分離し、変更を分離することで、ソフトウェアをより高い品質でより迅速に進化させることができます。 | 
|**コラボレーション**|パイプラインを使用すると、データ科学者は、機械学習の設計プロセスのすべてのリージョンで共同作業を行うことができます。さらに、パイプラインの複数のステップに同時に取り組むこともできます。|

## <a name="modules"></a>モジュール

パイプライン ステップでは以前の実行の結果を再利用できますが、ステップの構造は多くの場合、スクリプトと必要な依存ファイルがローカルで使用可能であることを想定しています。 データ サイエンティストが既存のコードの上に構築したい場合、スクリプトと依存関係を別のリポジトリから複製する必要がある場合が多いです。

モジュールはパイプライン ステップに似ていますが、ワークスペースによってバージョン管理が容易になるため、大規模なコラボレーションと再利用が可能になります。 モジュールは複数のパイプラインで再利用できるように設計されており、特定の計算がさまざまなユース ケースに適応されるように進化させることができます。 ユーザーは外部リポジトリを使用せずに、ワークスペースを通じて次のタスクを実行できます。

* 新しいモジュールを作成し、既存のモジュールの新しいバージョンを発行する
* 既存のバージョンを廃止する
* バージョンを無効にして、コンシューマーがそのバージョンを使用できないようにする
* 既定のバージョンを指定する
* チームが同じコードを使用していることを保証するために、ワークスペースからバージョン別にモジュールを取得する

Azure Machine Learning パイプラインでモジュールを作成、接続、および使用する方法のコード例については、[ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure ML パイプラインは、初期の開発段階で価値の提供を開始する強力な機能です。 チームとプロジェクトの規模が大きくなるにつれてその価値は増加します。 この記事では、Azure Machine Learning Python SDK でパイプラインを指定し、Azure で調整する方法について説明しました。 いくつかの基本的なソース コードについて説明し、使用可能な `PipelineStep` クラスをいくつか紹介しました。 Azure ML パイプラインを使用するタイミングと、Azure でそれらを実行する方法について理解しておく必要があります。 


+ [最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法を学習します。

+ [大量のデータに対してバッチ予測を実行する](tutorial-pipeline-batch-scoring-classification.md )方法を学習します。

+ [パイプライン コア](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)と[パイプライン ステップ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)については、SDK のリファレンス ドキュメントを参照してください。

+ [Azure Machine Learning パイプライン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)を紹介する Jupyter ノートブックの例を試します。 [ノートブックを実行してこのサービスを調べる](samples-notebooks.md)方法を学習します。
