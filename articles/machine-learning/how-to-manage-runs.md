---
title: Python でのトレーニングの実行の開始、監視、およびキャンセル
titleSuffix: Azure Machine Learning
description: 自分の機械学習の実験を、開始、状態設定、タグ付けおよび整理する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 0baee1646d2346a411b3b7bdb8efc90e9aeb7a4a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316877"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Python でのトレーニングの実行の開始、監視、およびキャンセル

[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)、[Machine Learning CLI](reference-azure-machine-learning-cli.md)、および [Azure Machine Learning Studio](https://ml.azure.com) には、自分のトレーニングおよび実験の実行を、監視、整理、管理するさまざまな方法があります。

この記事では、次のタスクの例を示します。

* 実行のパフォーマンスの監視。
* 実行のキャンセルまたは失敗。
* 子実行の作成。
* 実行のタグ付けおよび検索。

## <a name="prerequisites"></a>前提条件

次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* Azure Machine Learning SDK for Python (バージョン 1.0.21 以降)。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)に関する記事を参照してください。

    お使いの Azure Machine Learning SDK のバージョンを確認するには、次のコードを使用します。

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) と [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。

## <a name="monitor-run-performance"></a>実行のパフォーマンスの監視

* 実行とそのロギング プロセスを開始する

    # <a name="python"></a>[Python](#tab/python)
    
    1. お使いの実験を設定するには、[azureml.core](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py) パッケージから [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)、[Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py)、[Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) および [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) クラスをインストールします。
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) メソッドを使用して、そのログ プロセスを開始および実行します。
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    実験の実行を開始するには、次の手順を使用します。
    
    1. シェルまたはコマンド プロンプトから Azure CLI を使用して、Azure サブスクリプションに対して認証します。
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. ワークスペースの構成を、トレーニング スクリプトを含むフォルダーにアタッチします。 `myworkspace` は、ご利用の Azure Machine Learning ワークスペースに置き換えます。 `myresourcegroup` は、ワークスペースが含まれている Azure リソース グループに置き換えます。
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        このコマンドでは、サンプルの runconfig および conda 環境ファイルを含む `.azureml` サブディレクトリを作成します。 これには、Azure Machine Learning ワークスペースとの通信に使用される `config.json` ファイルも含まれています。
    
        詳しくは、「[az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)」をご覧ください。
    
    2. 実行を開始するには、次のコマンドを使用します。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach` コマンドで、2 つのサンプル runconfig ファイルを含む `.azureml` サブディレクトリが作成されました。
        >
        > プログラムで実行構成オブジェクトを作成する Python スクリプトがある場合は、[RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) を使用してそれを runconfig ファイルとして保存できます。
        >
        > runconfig ファイルのその他の例については、[https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) を参照してください。
    
        詳しくは、「[az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)」をご覧ください。
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    デザイナーでパイプラインの実行の送信を開始するには、以下の手順を使用します。
    
    1. パイプラインの既定のコンピューティング先を設定します。
    
    1. パイプライン キャンバス上部の **[実行]** を選択します。
    
    1. パイプラインの実行をグループ化する実験を選択します。
    
    ---

* 実行の状態を監視する

    # <a name="python"></a>[Python](#tab/python)
    
    * [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--) メソッドを使用して実行の状態を取得します。
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 実行 ID、実行時間、および実行に関する追加の詳細を取得するには、[`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) メソッドを使用します。
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 自分の実行が正常に終了したら、[`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) メソッドを使用し、それを完了とマークします。
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Python の `with...as` 設計パターンを使用している場合、実行が範囲外になると、実行によって自動的に実行が完了とマークされます。 ユーザーが手動で実行を完了とマークする必要はありません。
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 実験の実行の一覧を表示するには、次のコマンドを使用します。 `experiment` は、実験の名前に置き換えます。
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        このコマンドで、この実験の実行に関する情報を一覧表示する JSON ドキュメントが返されます。
    
        詳しくは、「[az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)」をご覧ください。
    
    * 特定の実行に関する情報を表示するには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます。
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        このコマンドで、その実行に関する情報を一覧表示する JSON ドキュメントが返されます。
    
        詳しくは、「[az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)」をご覧ください。
    
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    Studio で実験のアクティブな実行の数を表示するには、次の手順に従います。
    
    1. **[実験]** セクションに移動します。
    
    1. 実験を選択します。
    
        実験ページでは、アクティブなコンピューティング先の数と各実行の期間を確認できます。 
    
    1. 比較する実行を選択するか、グラフを追加するか、フィルターを適用することにより、実験のカスタマイズを行います。 これらの変更は **カスタム ビュー** として保存できるので、簡単に作業に戻ることができます。 ワークスペースのアクセス許可を持つユーザーは、カスタム ビューを編集または表示できます。 また、ブラウザーで URL をコピーして貼り付けることにより、カスタム ビューを他のユーザーと共有することもできます。  
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="スクリーンショット: カスタム ビューを作成する":::
    
    1. 特定の実行番号を選択します。
    
    1. **[ログ]** タブで、パイプラインの実行に関する診断ログとエラー ログを確認できます。
    
    ---
    
## <a name="cancel-or-fail-runs"></a>実行のキャンセルまたは失敗

間違いに気付いた場合、または実行の完了に時間がかかりすぎる場合は、実行をキャンセルできます。

# <a name="python"></a>[Python](#tab/python)

SDK を使用して実行をキャンセルするには、[`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--) メソッドを使用します。

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

実行が終了したが、実行にエラー (正しくないトレーニング スクリプトが使用されたなど) が含まれている場合、[`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) メソッドを使用して、それを失敗とマークできます。

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用して実行をキャンセルするには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

詳しくは、「[az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)」をご覧ください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

Studio で実行をキャンセルするには、次の手順を使用します。

1. **[実験]** または **[パイプライン]** セクションで、実行中のパイプラインに移動します。 

1. キャンセルするパイプラインの実行番号を選択します。

1. ツール バーで、 **[キャンセル]** を選択します

---

## <a name="create-child-runs"></a>子実行の作成

異なるハイパーパラメーターの調整を繰り返すなど、関連する実行をグループ化する場合に、子実行を作成できます。

> [!NOTE]
> 子実行は、SDK を使用してのみ作成できます。

このコード例では、`hello_with_children.py` スクリプトで [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) メソッドを使用して、渡された実行から 5 つの子実行のバッチを作成します。

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 子実行は、範囲外になると、自動的に完了とマークされます。

多数の子実行を効率よく作成するには、[`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) メソッドを使用します。 実行を作成するたびにネットワーク呼び出しが行われるため、実行のバッチを作成した方が、1 つずつ作成するよりも効率的です。

### <a name="submit-child-runs"></a>子実行を送信する

親実行から子実行を送信することもできます。 そうすることで、親実行と子実行の階層を作成できます。 

子実行で、親実行と異なる実行構成を使用することを望む場合があります。 たとえば、子に GPU ベースの構成を使用しながら、親に対して非力な CPU ベースの構成を使用できます。 他の一般的な目的は、各子に異なる引数とデータを渡すことです。 子実行をカスタマイズするには、子実行の `ScriptRunConfig` オブジェクトを作成します。 下のコードにより、次のことが行われます。

- `"gpu-cluster"` という名前のコンピューティング リソースを、ワークスペース `ws` から取得します
- 子 `ScriptRunConfig` オブジェクトに渡される異なる引数値を繰り返します
- カスタム コンピューティング リソースと引数を使用して、新しい子実行を作成して送信します
- すべての子実行が完了するまでブロックします

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

同じ構成、引数、および入力を使用して多数の子実行を効率的に作成するには、[`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) メソッドを使用します。 実行を作成するたびにネットワーク呼び出しが行われるため、実行のバッチを作成した方が、1 つずつ作成するよりも効率的です。

次のようにすれば、子実行内から親実行 ID を確認できます。

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>子実行を照会する

特定の親の子実行をクエリするには、[`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) メソッドを使用します。 ``recursive = True`` 引数を指定すると、入れ子になった子と孫のツリーを照会できます。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>実行のタグ付けおよび検索

Azure Machine Learning では、実行の整理にプロパティとタグを使用したり、自分の実行に対し重要な情報をクエリしたりできます。

* プロパティとタグの追加

    # <a name="python"></a>[Python](#tab/python)
    
    検索可能なメタデータを自分の実行に追加するには、[`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-) メソッドを使用します。 たとえば、次のコードでは実行に `"author"` プロパティが追加されます。
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    プロパティは変更不可であるため、プロパティによって監査目的の恒久的な記録が作成されます。 次のコード例は、前のコードで `"author"` プロパティ値として `"azureml-user"` が既に追加されているためエラーとなります。
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    プロパティとは異なり、タグは変更可能です。 自分の実験のコンシューマーに検索可能で意味のある情報を追加するには、[`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-) メソッドを使用します。
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    単純な文字列のタグを追加することもできます。 これらのタグがタグ辞書にキーとして表示されると、値は `None` になります。
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > CLI の使用では、タグの追加または更新のみが可能です。
    
    タグの追加または更新を行うには、次のコマンドを使用します。
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    詳しくは、「[az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)」をご覧ください。
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    Studio を使用するとプロパティとタグを表示することはできますが、変更することはできません。
    
    ---

* クエリ プロパティおよびタグ

    特定のプロパティとタグに一致する実行の一覧が返されるように、実験内の実行をクエリできます。

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI では、プロパティとタグに基づいて実行をフィルター処理するために使用できる [JMESPath](http://jmespath.org) クエリがサポートされています。 Azure CLI で JMESPath クエリを使用するには、`--query` パラメーターでそれを指定します。 次の例は、プロパティとタグを使用したクエリのいくつかを示しています。
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Azure CLI 結果のクエリ実行の詳細については、「[Azure CLI コマンドの出力のクエリ](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)」を参照してください。
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    1. **[パイプライン]** セクションに移動します。
    
    1. タグ、説明、実験名、および送信者名を使用してパイプラインをフィルター処理するには、検索バーを使用します。
    
    ---

## <a name="example-notebooks"></a>サンプルの Notebook

次のノートブックは、この記事の概念を示しています。

* ロギング API の詳細については、[ロギング API ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)に関するページを参照してください。

* Azure Machine Learning SDK での実行の管理の詳細については、[実行の管理ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* 自分の実験のメトリックのログを記録する方法の詳細については、[トレーニングの実行中にメトリックを記録する](how-to-track-experiments.md)に関するページを参照してください。
* Azure Machine Learning からリソースとログを監視する方法については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。