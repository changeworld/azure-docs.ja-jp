---
title: ML 実験の MLflow Tracking
titleSuffix: Azure Machine Learning
description: ML モデルのメトリックと成果物をログに記録するために、MLflow Tracking と Azure Machine Learning を設定します。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 8935412a7858dc8b3c4ab9b87e8990bb228c5977
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716336"
---
# <a name="track-ml-models-with-mlflow-and-azure-machine-learning"></a>MLflow と Azure Machine Learning を使用して ML モデルを追跡する

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を有効にして、MLflow 実験のバックエンドとして Azure Machine Learning に接続する方法について説明します。 

サポートされている機能は次のとおりです。 

+ [Azure Machine Learning ワークスペース](./concept-azure-machine-learning-architecture.md#workspace)で、実験のメトリックと成果物を追跡してログに記録します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。

+ [Azure Machine Learning バックエンド サポートを備えた MLflow Projects を使用してトレーニング ジョブを送信します (プレビュー)](how-to-train-mlflow-projects.md)。 Azure Machine Learning 追跡を使用してローカルにジョブを送信することも、[Azure Machine Learning コンピューティング](how-to-create-attach-compute-cluster.md)を介するなどして実行をクラウドに移行することもできます。

+ MLflow および Azure Machine Learning モデル レジストリでモデルを追跡および管理します。

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLFlow Tracking は MLflow のコンポーネントです。これは、実験の環境がローカル コンピューター、リモートのコンピューティング先、仮想マシン、[Azure Databricks クラスター](how-to-use-mlflow-azure-databricks.md)のいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。 

MLflow と Azure Machine Learning のその他の機能統合については、「[MLflow と Azure Machine Learning](concept-mlflow.md)」をご覧ください。

次の図は、MLflow Tracking を使用して、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存する例を示しています。

![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

> [!NOTE] 
> SQL ストレージ、サーバー、UI、またはデータ サイエンスの依存関係のない軽量 MLflow パッケージである [MLflow Skinny クライアント](https://github.com/mlflow/mlflow/blob/master/README_SKINNY.rst)を使用できます。 主に追跡機能とログ機能を必要とし、デプロイを含む MLflow の全機能はインポートしないユーザーには、これが推奨されます。 

## <a name="prerequisites"></a>前提条件

* `azureml-mlflow` パッケージをインストールします。 
    * このパッケージからは自動的に、MLflow がワークスペースにアクセスするための接続を提供する、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) の `azureml-core` が持ち込まれます。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。
    * [ワークスペースで MLflow 操作を実行するために必要なアクセス許可](how-to-assign-roles.md#mlflow-operations)を確認します。

## <a name="track-local-runs"></a>ローカル実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、ローカル実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) クラスをインポートします。

次のコードでは、`get_mlflow_tracking_uri()` メソッドによって、ワークスペース `ws` に一意の追跡 URI アドレスを割り当て、`set_tracking_uri()` によって、MLflow の追跡 URI がそのアドレスを参照するよう設定します。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追跡 URI の有効期間は最大 1 時間以内です。 一定のアイドル時間の経過後にスクリプトを再起動した場合は、get_mlflow_tracking_uri API を使用して新しい URI を取得してください。

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>リモート実行の追跡

リモート実行では、GPU 対応仮想マシンや Machine Learning コンピューティング クラスターなど、より強力なコンピューティングでモデルをトレーニングできます。 さまざまなコンピューティング オプションについては、「[モデル トレーニング用のコンピューティング ターゲットを使用する](how-to-set-up-training-targets.md)」をご覧ください。

Azure Machine Learning で MLflow Tracking を使用すると、リモート実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。 その中で MLflow 追跡コードを実行すると、メトリックのログが自動的にワークスペースに記録されます。 

次のサンプル conda 環境にはパッケージとして `mlflow` と `azureml-mlflow` が含まれています。 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - mlflow
    - numpy
```

お使いのスクリプトで、[`Environment`](/python/api/azureml-core/azureml.core.environment.environment) クラスを使用し、コンピューティングとトレーニングの実行環境を構成します。 次に、コンピューティング ターゲットとして、リモート コンピューティングで [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) を構築します。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

このコンピューティングおよびトレーニングの実行構成では、`Experiment.submit()` メソッドを使用して実行を送信します。 この方法により、MLflow の追跡 URI が自動的に設定され、MLflow からワークスペースにログが送信されます。

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>ワークスペースでのメトリックと成果物の表示

MLflow ログ記録のメトリックと成果物は、お使いのワークスペースに保持されます。 それらを随時表示するには、[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースに移動し、ワークスペースで名前によって実験を見つけます。  または、次のコードを実行します。 

```python
run.get_metrics()
```

## <a name="manage-models"></a>モデルを管理する 

MLflow モデル レジストリをサポートする [Azure Machine Learning モデル レジストリ](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)にモデルを登録して追跡します。 Azure Machine Learning モデルは、MLflow モデル スキーマに対応しているため、これらのモデルをさまざまなワークフロー間で容易にエクスポートおよびインポートすることができます。 実行 ID などの MLflow 関連のメタデータには、追跡可能性のために、登録されたモデルのタグも付けられます。 ユーザーは、トレーニングの実行を送信し、MLflow の実行から生成されたモデルを登録およびデプロイすることができます。 

運用準備が整ったモデルを 1 つの手順でデプロイおよび登録したい場合は、「[MLflow モデルのデプロイと登録](how-to-deploy-mlflow-models.md)」を参照してください。

実行からのモデルを登録して表示するには、次の手順に従います。

1. 実行が完了したら、`register_model()` メソッドを呼び出します。

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) を使用してワークスペースに登録されたモデルを表示します。

    次の例では、登録されたモデル `my-model` の MLflow 追跡メタデータがタグ付けされています。 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. **[成果物]** タブを選択すると、MLflow モデル スキーマ (conda.yaml、MLmodel、model.pkl) に対応するすべてのモデル ファイルが表示されます。

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. [MLmodel] を選択すると、実行によって生成された MLmodel ファイルが表示されます。

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](./media/how-to-use-mlflow/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure ML ノートブックでの MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) は、この記事で提示した概念を示し、さらに詳しく説明します。

> [!NOTE]
> mlflow を使用したコミュニティ主導の例のリポジトリについては、 https://github.com/Azure/azureml-examples を参照してください。

## <a name="next-steps"></a>次のステップ

* [MLflow を使用してモデルをデプロイします](how-to-deploy-mlflow-models.md)。
* [データの誤差](./how-to-enable-data-collection.md)について実稼働モデルを監視します。
* [MLflow を使用して Azure Databricks 実行を追跡する](how-to-use-mlflow-azure-databricks.md)。
* [モデルを管理します](concept-model-management-and-deployment.md)。
