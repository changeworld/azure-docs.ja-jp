---
title: メトリックおよびログ ファイルの記録および表示
titleSuffix: Azure Machine Learning
description: ML のトレーニング実行のログ記録を有効にすると、リアルタイムの実行メトリックを監視し、エラーと警告を診断するのに役立ちます。
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 81f05686954e35d68d93c598978e9676652fe71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593479"
---
# <a name="log--view-metrics-and-log-files"></a>メトリックおよびログ ファイルの記録および表示

既定の Python ログ パッケージと Azure Machine Learning Python SDK 固有の機能の両方を使用してリアルタイムの情報をログに記録します。 ユーザーは、ポータルでローカルにログを記録し、ワークスペースにログを送信することができます。

ログは、エラーや警告を診断したり、パラメーターやモデルのパフォーマンスなどのパフォーマンス メトリックを追跡したりするのに役立ちます。 この記事では、次のシナリオでログ記録を有効にする方法について説明します。

> [!div class="checklist"]
> * 実行メトリックのログ記録
> * 対話型のトレーニング セッション
> * ScriptRunConfig を使用したトレーニング ジョブの送信
> * Python ネイティブの `logging` 設定
> * その他のソースからのログ記録


> [!TIP]
> この記事では、モデルのトレーニング プロセスを監視する方法について説明します。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある場合は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="data-types"></a>データ型

複数のデータ型 (スカラー値、リスト、テーブル、イメージ、ディレクトリなど) をログに記録できます。 詳細と、さまざまなデータ型の Python コード例については、[Run クラスの参照ページ](/python/api/azureml-core/azureml.core.run%28class%29)を参照してください。

## <a name="logging-run-metrics"></a>実行メトリックのログ 

ログ API で次のメソッドを使用して、メトリックの視覚化に影響を与えます。 これらのログに記録されたメトリックの[サービスの制限](./resource-limits-quotas-capacity.md#metrics)に注意してください。 

|ログに記録される値|コード例| ポータルでの形式|
|----|----|----|
|数値の配列をログに記録します| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|単一変数の折れ線グラフ|
|(for ループ内からのように) 繰り返し使用される、同じメトリック名を持つ単一数値をログに記録します| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 単一変数の折れ線グラフ|
|2 つの数値列が繰り返し含まれる 1 行をログに記録します|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|変数が 2 つの折れ線グラフ|
|2 つの数値列を含むテーブルをログに記録します|`run.log_table(name='Sine Wave', value=sines)`|変数が 2 つの折れ線グラフ|
|イメージをログに記録します|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|このメソッドを使用してイメージ ファイルをログに記録するか、matplotlib を使用して実行にプロットします。 これらのイメージは実行レコードで表示して比較できます|

## <a name="logging-with-mlflow"></a>MLflow を使用したログ

モデル、メトリック、および成果物のログ記録には、ローカル モードからクラウドへの移植性をサポートしているオープン ソースの MLflow の使用をお勧めします。 次の表とコード例は、MLflow を使用してトレーニングの実行からメトリックと成果物をログに記録する方法を示しています。 
[MLflow のログ記録方法と設計パターンの詳細を確認してください](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.log_artifact)。

必ず `mlflow` と `azureml-mlflow` の pip パッケージをお使いのワークスペースにインストールしてください。 

```conda
pip install mlflow
pip install azureml-mlflow
```

MLflow トラッキング URI が Azure Machine Learning バックエンドを指すように設定して、メトリックと成果物がワークスペースに記録されるようにします。 

```python
from azureml.core import Workspace
import mlflow
from mlflow.tracking import MlflowClient

ws = Workspace.from_config()
mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())

mlflow.create_experiment("mlflow-experiment")
mlflow.set_experiment("mlflow-experiment")
mlflow_run = mlflow.start_run()
```

|ログに記録される値|コード例| メモ|
|----|----|----|
|数値 (int または float) をログに記録する | `mlflow.log_metric('my_metric', 1)`| |
|ブール値をログに記録する | `mlflow.log_metric('my_metric', 0)`| 0 = True、1 = False|
|文字列をログに記録する | `mlflow.log_text('foo', 'my_string')`| 成果物としてログに記録されます|
|numpy メトリックまたは PIL 画像オブジェクトをログに記録する|`mlflow.log_image(img, 'figure.png')`||
|matlotlib プロットまたは画像ファイルをログに記録する|` mlflow.log_figure(fig, "figure.png")`||

## <a name="view-run-metrics-via-the-sdk"></a>SDK を使用して実行メトリックを表示する
`run.get_metrics()` を使用して、トレーニング済みモデルのメトリックを表示できます。 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping metric names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

MLflow では、実行オブジェクトのデータと情報のプロパティを介して実行情報にアクセスすることもできます。 詳細については、[MLflow.entities.Run オブジェクト](https://mlflow.org/docs/latest/python_api/mlflow.entities.html#mlflow.entities.Run)のドキュメントを参照してください。 

実行が完了したら、MlFlowClient() を使用してこれを取得できます。

```python
from mlflow.tracking import MlflowClient

# Use MlFlow to retrieve the run that was just completed
client = MlflowClient()
finished_mlflow_run = MlflowClient().get_run(mlflow_run.info.run_id)
```

実行オブジェクトのデータ フィールドで、実行のメトリック、パラメーター、およびタグを表示できます。

```python
metrics = finished_mlflow_run.data.metrics
tags = finished_mlflow_run.data.tags
params = finished_mlflow_run.data.params
```

>[!NOTE]
> `mlflow.entities.Run.data.metrics` の下のメトリック辞書は、指定されたメトリック名に対して最後にログに記録された値のみを返します。 たとえば、`sample_metric` という名前のメトリックに対して 1、2、3、4 の順にログが記録された場合、`sample_metric` のメトリック辞書に含まれるのは 4 のみです。
> 
> 特定のメトリック名に対してログに記録されたすべてのメトリックを取得するには、[`MlFlowClient.get_metric_history()`](https://www.mlflow.org/docs/latest/python_api/mlflow.tracking.html#mlflow.tracking.MlflowClient.get_metric_history) を使用できます。

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-the-studio-ui"></a>スタジオの UI で実行メトリックを表示する

ログに記録されたメトリックを含む、完了した実行レコードを参照するには、[Azure Machine Learning Studio](https://ml.azure.com) を使用します。

**[実験]** タブに移動します。複数の実験にわたってワークスペース内のすべての実行を表示するには、 **[すべての実行]** タブを選択します。上部のメニュー バーに実験フィルターを適用することで、特定の実験の実行をドリルダウンできます。

個々の実験ビューで、 **[すべての実験の]** タブを選択します。実験実行ダッシュボードでは、実行ごとに追跡されたメトリックとログを確認できます。 

また、実行一覧テーブルを編集して、複数の実行を選択し、特定の実行に関してログに記録された直近の値や、最小値、最大値を表示することができます。 グラフをカスタマイズして、ログに記録されたメトリック値と集計を複数の実行にわたって比較します。 グラフの y 軸に複数のメトリックをプロットし、x 軸をカスタマイズしてログ記録されたメトリックをプロットすることができます。 


### <a name="view-and-download-log-files-for-a-run"></a>実行のログ ファイルを表示およびダウンロードする 

ログ ファイルは、Azure ML ワークロードをデバッグするための必須リソースです。 トレーニング ジョブを送信した後、特定の実行にドリルダウンしてそのログと出力を表示します。  

1. **[実験]** タブに移動します。
1. 特定の実行の runID を選択します。
1. ページの上部にある **[出力とログ]** を選択します。
2. **[すべてダウンロード]** を選択して、すべてのログを zip フォルダーにダウンロードします。
3. 個々のログ ファイルをダウンロードするには、ログ ファイルを選択して **[ダウンロード]** を選択します。

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="実行の [出力とログ] セクションのスクリーンショット。":::

次の表は、このセクションに表示されるフォルダー内のログ ファイルの内容を示しています。

> [!NOTE]
> 実行ごとにすべてのファイルが表示されるとは限りません。 たとえば、20_image_build_log*.txt は、新しいイメージが作成されたとき (環境を変更した場合など) にのみ表示されます。

#### <a name="azureml-logs-folder"></a>`azureml-logs` フォルダー

|ファイル  |説明  |
|---------|---------|
|20_image_build_log.txt     | トレーニング環境用 Docker イメージ ビルド ログ (省略可能、実行ごとに 1 つ)。 環境を更新する場合にのみ適用されます。 それ以外の場合、AML ではキャッシュされたイメージを再利用します。 成功した場合は、対応するイメージのイメージ レジストリの詳細が含められます。         |
|55_azureml-execution-<node_id>.txt     | ホスト ツールの stdout/stderr ログ (ノードごとに 1 つ)。 イメージをコンピューティング ターゲットにプルします。 このログは、コンピューティング リソースをセキュリティで保護した後にのみ表示されることに注意してください。         |
|65_job_prep-<node_id>.txt     |   ジョブ準備スクリプトの stdout/stderr ログ (ノードごとに 1 つ)。 コンピューティング ターゲットとデータストア (要求された場合) にコードをダウンロードします。       |
|70_driver_log(_x).txt      |  AML コントロール スクリプトとカスタマー トレーニング スクリプトからの stdout/stderr ログ (プロセスごとに 1 つ)。 **スクリプトからの標準出力。このファイルに、コードのログ (print ステートメントなど) が表示されます。** ほとんどの場合は、ここでログを監視します。       |
|70_mpi_log.txt     |   MPI フレームワーク ログ (省略可能、実行ごとに 1 つ)。 MPI 実行の場合のみ。   |
|75_job_post-<node_id>.txt     |  ジョブ リリース スクリプトの stdout/stderr ログ (ノードごとに 1 つ)。 ログを送信し、コンピューティング リソースを解放して Azure に戻します。        |
|process_info.json      |   どのプロセスがどのノードで実行されているかを示します。  |
|process_status.json      | プロセスが開始されていないか、実行中か、完了しているかといったプロセスの状態を示します。         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` フォルダー

|ファイル  |説明  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   ジョブ準備のためのシステム ログ        |
|job_release_azureml.log     | ジョブ解放のためのシステム ログ        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` フォルダー

サイドカーが有効になっている場合、ジョブ準備とジョブ解放のスクリプトが、サイドカー コンテナー内で実行されます。  ノードごとに 1 つのフォルダーがあります。 

|ファイル  |説明  |
|---------|---------|
|start_cms.txt     |  サイドカー コンテナーの開始時に開始されるプロセスのログ       |
|prep_cmd.txt      |   `job_prep.py` の実行時に入力された ContextManagers のログ (この内容の一部が `azureml-logs/65-job_prep` にストリーミングされます)       |
|release_cmd.txt     |  `job_release.py` の実行時に終了した ComtextManagers のログ        |

#### <a name="other-folders"></a>その他のフォルダー

マルチコンピューティング クラスターでのジョブのトレーニングでは、ノード IP ごとにログが存在します。 各ノードの構造は、単一ノードのジョブと同じです。 execution ログ、stderr ログ、stdout ログ全般に対して 1 つのログ フォルダーが追加されています。

Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録されます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。


## <a name="interactive-logging-session"></a>対話型のログ セッション

対話型のログ セッションは、通常、ノートブック環境で使用されます。 メソッド [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) を使用すると、対話型のログ セッションが開始されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。 メソッド [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) を使用すると、セッションが終了し、実行が完了としてマークされます。

## <a name="scriptrun-logs"></a>ScriptRun ログ

このセクションでは、ScriptRunConfig を使用して構成したときに作成される実行の内部にログ記録のコードを追加する方法について説明します。 [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) クラスを使用して、反復可能な実行のためにスクリプトと環境をカプセル化できます。 このオプションを使用して、監視用の視覚的な Jupyter Notebook ウィジェットを表示することもできます。

この例では、アルファ値に対してパラメーター スイープを実行し、[run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----) メソッドを使用して結果をキャプチャします。

1. ログ記録ロジック `train.py` を含むトレーニング スクリプトを作成します。

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. ```train.py``` スクリプトを送信して、ユーザー管理の環境内で実行します。 スクリプト フォルダー全体がトレーニング用に送信されます。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output` パラメーターを使用すると詳細なログ記録が有効になり、これによってトレーニング プロセスからの詳細情報と、リモート リソースまたはコンピューティング先に関する情報を見ることができます。 次のコードを使用して、実験を送信するときに詳細ログ記録を有効にします。

```python
run = exp.submit(src, show_output=True)
```

結果の実行時に `wait_for_completion` 関数で同じパラメーターを使用することもできます。

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>ネイティブの Python のログ

SDK の一部のログには、ログ レベルを DEBUG に設定するよう指示するエラーが含まれる場合があります。 ログ記録のレベルを設定するには、スクリプトに次のコードを追加します。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>他のログ記録ソース

Azure Machine Learning では、トレーニング中に、自動機械学習の実行や、ジョブを実行する Docker コンテナーなど、他のソースからの情報をログに記録することもできます。 これらのログは文書化されていませんが、問題が発生して Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

Azure Machine Learning デザイナーにおけるメトリックのログ記録の詳細については、[デザイナーでメトリックをログに記録する方法](how-to-track-designer-experiments.md)に関する記事を参照してください

## <a name="example-notebooks"></a>サンプルの Notebook

次の Notebook は、この記事の概念を示しています。
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の使用方法の詳細については、これらの記事を参照してください。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。
