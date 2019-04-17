---
title: Python でのトレーニングの実行の開始、監視、およびキャンセル
titleSuffix: Azure Machine Learning service
description: 自分の機械学習の実験を、開始、状態設定、タグ付けおよび整理する方法を学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471496"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Python でのトレーニングの実行の開始、監視、およびキャンセル

[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) には、自分のトレーニングおよび実験の実行を、監視、整理、管理するさまざまな方法があります。

ここでは、次のタスクの例を示します。

* [実行のパフォーマンスの監視](#monitor)
* [実行のキャンセルまたは失敗](#cancel)
* [子実行の作成](#children)
* [実行のタグ付けおよび検索](#tag)

## <a name="prerequisites"></a>前提条件

次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* Azure Machine Learning ワークスペース。 「[Create an Azure Machine Learning service workspace](setup-create-workspace.md)」(Azure Machine Learning service ワークスペースを作成する) を参照してください。

* Azure Machine Learning SDK for Python のインストール (バージョン 1.0.21 以降)。 SDK の最新バージョンをインストールまたはアップグレードするには、[Install/update the SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (SDK のインストール/更新) ページに移動してください。

    お使いの Azure Machine Learning SDK のバージョンを確認するには、次のコードを使用します。

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>実行の開始とその状態の設定

お使いの実験を設定するには、[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) パッケージから [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) および [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) クラスをインストールします。

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) メソッドを使用して、そのログ プロセスを開始および実行します。

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) でその実行の状態を取得します。

```Python
print(notebook_run.get_status())
```

実行の詳細をさらに取得するには、[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) を使用します。

```Python
notebook_run.get_details()
```

自分の実行が正常に終了したら、[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) メソッドを使用し、それを完了とマークします。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Python の `with...as` のパターンを使用することも可能です。 このコンテキストでは、実行が範囲外となった場合に、実行によって自動的に実行が完了とマークされます。 この方法では、ユーザーが実行を手動で完了とマークする必要はありません。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>実行のキャンセルまたは失敗

 間違いを発見したり、実行の完了にしばらく時間がかかると思う場合、[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) メソッドを使用して、完了前にその実行を停止して、キャンセルとマークできます。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

終了した実行に、不正なトレーニング スクリプトが使用されたなどのエラーが表示される場合、[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) メソッドを使用して、それが失敗したとマークできます。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>子実行の作成

異なるハイパーパラメーターの調整を繰り返すなど、関連する実行をグループ化する場合に、子実行を作成できます。

このコード例では、hello_with_children.py スクリプトを使用して、[`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) メソッドを使用して、渡された実行から 5 つの子実行のバッチを作成します。

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 子実行は、範囲外になると自動的に完了します。

子実行は 1 つずつ実行することも可能ですが、作成されるたびにネットワーク呼び出しが行われるので、実行をバッチ送信するよりも効率が下がります。

特定の親の子実行をクエリする場合、[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) メソッドを使用します。

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>実行のタグ付けおよび検索

Azure Machine Learning service では、実行の整理にプロパティとタグを使用したり、自分の実行に対し重要な情報をクエリしたりできます。

### <a name="add-properties-and-tags"></a>プロパティとタグの追加

[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) を使用すると、自分の実行に検索可能なメタデータを追加できます。 たとえば、次のコードでは実行に "author" プロパティが追加されます。

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

プロパティは変更可能なため、監査目的の永続的な記録として有用です。 次のコード例は、前のコードで "author" プロパティとして "azureml-user" が既に追加されているためエラーとなります。

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

ただし、タグは変更可能です。 [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) を使用すると、自分の実験のコンシューマーに検索可能で意味のある情報を追加できます。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

単純な文字列のタグを追加することも可能です。 これは、`None` の値のタグ辞書に表示されます。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>クエリ プロパティおよびタグ

特定のプロパティおよびタグと一致する、実験内の実行をクエリできます。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>サンプルの Notebook

次の Notebook は、この記事の概念を示しています。

* ログ API の詳細については、[ログ API ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)に関するページを参照してください。

* Azure Machine Learning SDK 内での実行の管理の詳細については、[manage runs ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* 自分の実験のメトリックのログを記録する方法の詳細については、[トレーニングの実行中にメトリックを記録する](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment)に関するページを参照してください。