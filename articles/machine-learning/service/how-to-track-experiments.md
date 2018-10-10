---
title: 実験とトレーニング メトリックを追跡する - Azure Machine Learning | Microsoft Docs
description: Azure Machine Learning service では、実験を追跡し、メトリックを監視して、モデルの作成プロセスを拡張できます。 トレーニング スクリプトにログ記録を追加する方法、実験を送信する方法、実行中のジョブの進行状況を確認する方法、および実行の結果を表示する方法について説明します。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: ced10a54d569531b06ee47b646130f43cedd2963
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984604"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Azure Machine Learning で実験とトレーニング メトリックを追跡する

Azure Machine Learning service では、実験を追跡し、メトリックを監視して、モデルの作成プロセスを拡張できます。 この記事では、トレーニング スクリプトにログ記録を追加するさまざまな方法、**start_logging** と **ScriptRunConfig** で実験を送信する方法、実行中のジョブの進行状況を確認する方法、および実行の結果を表示する方法について説明します。 

## <a name="list-of-training-metrics"></a>トレーニング メトリックの一覧 

実験のトレーニング中に、次のメトリックを実行に追加できます。 実行で追跡できる内容の詳細な一覧については、[SDK リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure/azure-ml-sdk-overview?view=azure-ml-py)をご覧ください。

|type| Python 関数 | メモ|
|----|:----:|:----:|
|スカラー値 | `run.log(name, value, description='')`| 指定した名前で実行にメトリック値を記録します。 実行にメトリックを記録すると、メトリックは実験の実行レコードに格納されます。  同じメトリックを実行内で複数回記録でき、結果はそのメトリックのベクターと見なされます。|
|リスト| `run.log_list(name, value, description='')`|指定した名前で実行にリスト メトリック値を記録します。|
|行| `run.log_row(name, description=None, **kwargs)`|*log_row* を使用すると、kwargs で記述されているように、列を含むテーブル メトリックが作成されます。 各名前付きパラメーターにより、指定した値の列が生成されます。  *log_row* を 1 回呼び出すと任意のタプルを記録でき、ループ内で複数回呼び出すと完全なテーブルを生成できます。|
|テーブル| `run.log_table(name, value, description='')`| 指定した名前で実行にテーブル メトリックを記録します。 |
|イメージ| `run.log_image(name, path=None, plot=None)`|実行レコードにイメージ メトリックを記録します。 log_image を使用してイメージ ファイルに記録するか、または matplotlib を使用して実行にプロットします。  これらのイメージは実行レコードで表示して比較できます。|
|実行にタグを付ける| `run.tag(key, value=None)`|文字列キーと省略可能な文字列値で実行にタグを付けます。|
|ファイルまたはディレクトリをアップロードする|`run.upload_file(name, path_or_stream)`|実行レコードにファイルをアップロードします。 実行は指定された出力ディレクトリ内のファイルを自動的にキャプチャします。ディレクトリの既定値は、ほとんどの実行種類で "./outputs" です。  追加のファイルをアップロードする必要がある場合、または出力ディレクトリが指定されていない場合にのみ、upload_file を使用します。 出力ディレクトリにアップロードされるように、名前に `outputs` を追加することをお勧めします。 `run.get_file_names()` を呼び出すことにより、この実行レコードに関連付けられているすべてのファイルの一覧を取得できます。|

> [!NOTE]
> スカラー、リスト、行、およびテーブルのメトリックの型には、float、integer、または string を使用できます。

## <a name="log-metrics-for-experiments"></a>実験のメトリックを記録する

実験を追跡または監視する場合は、実行を送信するときにログ記録を開始するコードを追加する必要があります。 実行の送信をトリガーする方法を以下に示します。
* __Run.start_logging__ - トレーニング スクリプトにログ関数を追加し、指定した実験で対話型のログ セッションを開始します。 **start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。
* __ScriptRunConfig__ - トレーニング スクリプトにログ関数を追加し、実行でスクリプト フォルダー全体を読み込みます。  **ScriptRunConfig** は、スクリプト実行の構成をセットアップするためのクラスです。 このオプションでは、監視コードを追加して、完了の通知を受け取るか、または監視するためのビジュアル ウィジェットを取得できます。

## <a name="set-up-the-workspace-and-experiment"></a>ワークスペースと実験を設定する
ログを追加して実験を送信する前に、ワークスペースと実験を設定する必要があります。

1. ワークスペースを読み込みます。 ワークスペースの構成の設定について詳しくは、[クイック スタート](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started)に従ってください。

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. 実験を作成します。

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  exp = Experiment(workspace_object = ws, name = experiment_name)
  ```
  
## <a name="option-1-use-startlogging"></a>オプション 1: start_logging を使用する

**start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。

次の例では、ローカルの Jupyter Notebook でローカルに単純な sklearn Ridge モデルをトレーニングします。 さまざまな環境への実験の送信について詳しくは、「[Azure Machine Learning サービスでモデルのトレーニング用のコンピューティング ターゲットを設定する](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)」をご覧ください。

1. ローカルの Jupyter Notebook でトレーニング スクリプトを作成します。 

  ``` python
  # load diabetes dataset, a well-known small dataset that comes with scikit-learn
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from sklearn.externals import joblib

  X, y = load_diabetes(return_X_y = True)
  columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
  }
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl');
  ```

2. Azure Machine Learning service SDK を使用して実験の追跡を追加し、永続化されたモデルを実験の実行レコードにアップロードします。 次のコードでは、タグを追加し、ログを記録して、実験の実行にモデル ファイルをアップロードします。

  ```python
  run = Run.start_logging(experiment = exp)
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

スクリプトが ```run.complete()``` で終了すると、実行は完了としてマークされます。  これは通常、対話型ノートブックのシナリオで使用されます。

## <a name="option-2-use-scriptrunconfig"></a>オプション 2: ScriptRunConfig を使用する

**ScriptRunConfig** は、スクリプト実行の構成をセットアップするためのクラスです。 このオプションでは、監視コードを追加して、完了の通知を受け取るか、または監視するためのビジュアル ウィジェットを取得できます。

この例は、上記の基本的な sklearn Ridge モデルを拡張します。 単純なパラメーター スイープを行ってモデルのアルファ値をスイープし、実行でのメトリックとトレーニング済みモデルを実験にキャプチャします。 例は、ユーザー管理の環境に対してローカルに実行します。 

1. トレーニング スクリプトを作成します。 ```%%writefile%%``` を使用して、トレーニング コードを ```train.py``` としてスクリプト フォルダーに書き出します。

  ```python
  %%writefile $project_folder/train.py

  import os
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from azureml.core.run import Run
  from sklearn.externals import joblib

  import numpy as np

  #os.makedirs('./outputs', exist_ok = True)

  X, y = load_diabetes(return_X_y = True)

  run = Run.get_submitted_run()

  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

  # list of numbers from 0.0 to 1.0 with a 0.05 interval
  alphas = np.arange(0.0, 1.0, 0.05)

  for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
  ```

2. ```train.py``` スクリプトは ```mylib.py``` を参照しています。 このファイルにより、Ridge モデルで使用するアルファ値の一覧を取得できます。

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. ユーザー管理のローカル環境を構成します。

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. ```train.py``` スクリプトを送信して、ユーザー管理の環境内で実行します。 ```mylib.py``` ファイルも含めて、このスクリプト フォルダー全体がトレーニングのために送信されます。

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_user_managed)
  run = exp.submit(src)
  ```
  
## <a name="view-run-details"></a>実行の詳細を表示する

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Jupyter Notebook ウィジェットで実行を監視する
**ScriptRunConfig** メソッドを使用して実行を送信するときに、Jupyter Notebook ウィジェットを使用して実行の進行状況を見ることができます。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで 10 秒から 15 秒ごとにライブ更新を提供します。

1. 実行が完了するのを待っている間、Jupyter ウィジェットを表示します。

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Jupyter Notebook ウィジェットのスクリーンショット](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>完了時にログの結果を取得する

モデルのトレーニングと監視はバックグラウンドで行われるので、待機中に他のタスクを実行できます。 また、モデルのトレーニングが完了するまで待ってから、さらにコードを実行することもできます。 **ScriptRunConfig** を使用するときは、```run.wait_for_completion(show_output = True)``` を使用してモデルのトレーニングの完了を表示できます。 ```show_output``` フラグを指定すると、詳細な出力が提供されます。 
  
### <a name="query-run-metrics"></a>実行のメトリックのクエリを行う

```run.get_metrics()``` を使用して、トレーニング済みモデルのメトリックを表示できます。 上の例でログに記録されたすべてのメトリックを取得して、最適なモデルを決定できます。

## <a name="view-the-experiment-in-the-azure-portal"></a>Azure portal で実験を表示する

実験の実行が完了したら、記録された実験の実行レコードを参照できます。 次の 2 つの方法で行います。

* ```print(run.get_portal_url())``` で実行に対する URL を直接取得します
* 実行の名前を送信することで、実行の詳細を表示します (この場合は ```run```)。 実験名、ID、種類、状態、詳細ページ、Azure portal へのリンク、およびドキュメントへのリンクが示されます。

実行へのリンクでは、Azure portal 内の実行詳細ページに直接移動します。 ここでは、任意のプロパティ、追跡対象のメトリック、イメージ、および実験に記録されたグラフを見ることができます。 この場合は、MSE とアルファ値を記録しました。

  ![Azure portal での実行の詳細のスクリーンショット](./media/how-to-track-experiments/run-details-page-web.PNG)

また、実行に対する出力またはログを表示したり、送信した実験のスナップショットをダウンロードして他のユーザーと実験フォルダーを共有したりすることもできます。

## <a name="example-notebooks"></a>サンプルの Notebook
次の Notebook は、この記事の概念を示しています。
* `01.getting-started/01.train-within-notebook/01.train-within-notebook.ipynb`
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`

これらの Notebook を取得するには: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

次の手順を試して、Azure Machine Learning SDK for Python を使用する方法を学習してください。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。

* [Azure Machine Learning で PyTorch モデルをトレーニングする](how-to-train-pytorch.md)方法を学習してください。
