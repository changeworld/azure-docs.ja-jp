---
title: 'クイック スタート: Python SDK を使用して機械学習サービス ワークスペースを作成する - Azure Machine Learning'
description: Azure Machine Learning の利用を開始します。 Python SDK をインストールし、それを使用してワークスペースを作成します。 このワークスペースが、Azure Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: cc348ca50b942b6b8b1474ed4dac4067d107a4af
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378001"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>クイック スタート: Python を使用して Azure Machine Learning の利用を開始する

このクイック スタートでは、Azure Machine Learning SDK for Python を使用して Machine Learning サービスの[ワークスペース](concept-azure-machine-learning-architecture.md)を作成して使用します。 このワークスペースが、Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。

このチュートリアルでは、Python SDK をインストールし、以下の操作を行います。

* Azure サブスクリプションでワークスペースを作成します。
* 後で他のノートブックやスクリプトで使用するために、そのワークスペースの構成ファイルを作成する。
* ワークスペース内の値をログに記録するためのコードを記述する。
* ログに記録した値をワークスペースで表示する。

このクイック スタートでは、ワークスペースと構成ファイルを作成します。 それらは、他の Machine Learning チュートリアルとハウツー記事の前提条件として使用できます。 他の Azure サービスと同様に、Machine Learning にも、関連付けられている制限とクォータがあります。 クォータの説明と、クォータの引き上げを要求する方法については、[こちら](how-to-manage-quotas.md)を参照してください。

以下の Azure リソースは、リージョンで利用可能になると、ワークスペースに自動的に追加されます。
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。


## <a name="install-the-sdk"></a>SDK のインストール

2018 年 9 月 27 日より後に作成されたデータ サイエンス仮想マシンを使用する場合は、このセクションをスキップします。** これらのデータ サイエンス仮想マシンには、Python SDK がプレインストールされています。

SDK をインストールする前に、分離された Python 環境を作成することをお勧めします。 このクイック スタートでは [Miniconda](https://conda.io/docs/user-guide/install/index.html) を使用していますが、フル インストールした [Anaconda](https://www.anaconda.com/) や [Python virtualenv](https://virtualenv.pypa.io/en/stable/) を使用することもできます。

### <a name="install-miniconda"></a>Miniconda のインストール


Miniconda を[ダウンロード](https://conda.io/miniconda.html)してインストールします。 Python 3.7 以降のバージョンを選択します。 Python 2.x バージョンは選択しないでください。

### <a name="create-an-isolated-python-environment"></a>分離された Python 環境の作成 

コマンド ライン ウィンドウを開きます。 次に、Python 3.6 で `myenv` という名前の新しい conda 環境を作成します。

```sh
conda create -n myenv -y Python=3.6
```

環境をアクティブにします。

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>SDK のインストール

アクティブにした conda 環境に SDK をインストールします。 このコードによって、Machine Learning SDK の主要なコンポーネントがインストールされます。 `myenv` conda 環境に Jupyter Notebook サーバーもインストールされます。 インストールは、完了までに**約 4 分**かかります。

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>ワークスペースの作成

Jupyter Notebook を起動するには、このコマンドを入力します。
```sh
jupyter notebook
```

ブラウザー ウィンドウで、既定の `Python 3` カーネルを使用して新しいノートブックを作成します。 

SDK のバージョンを表示するには、ノートブックのセルに次の Python コードを入力し、それを実行します。

```python
import azureml.core
print(azureml.core.VERSION)
```

新しい Azure リソース グループと新しいワークスペースを作成します。

[Azure portal のサブスクリプションの一覧](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、`<azure-subscription-id>` の値を見つけます。 自分のロールが所有者または共同作成者になっている任意のサブスクリプションを使用します。

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

上記のコードを実行すると、お客様の Azure アカウントにサインインするための新しいブラウザー ウィンドウが起動される場合があります。 サインインすると、認証トークンがローカルにキャッシュされます。

関連付けられているストレージ、コンテナー レジストリ、キー コンテナーなど、ワークスペースの詳細を表示するには、次のコードを入力します。

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>構成ファイルの記述

構成ファイル内のワークスペースの詳細を現在のディレクトリに保存します。 このファイルは、"aml_config\config.json" という名前です。  

このワークスペース構成ファイルを使用すると、後でこの同じワークスペースを簡単に読み込むことができます。 同じディレクトリまたはサブディレクトリ内の他のノートブックやスクリプトと共に読み込むことができます。 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

`write_config()` API 呼び出しでは、構成ファイルが現在のディレクトリに作成されます。 `config.json` ファイルには、以下のスクリプトが含まれています。

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>ワークスペースの使用

実験の実行を追跡するために、SDK の基本的な API が使用されるコードを記述します。

```python
from azureml.core import Experiment

# create a new experiment
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>ログに記録された結果の表示
実行が完了したら、Azure portal で実験の実行を表示できます。 次のコードを使用して、前回の実行に関する結果への URL を表示します。

```python
print(run.get_portal_url())
```

リンクを使用すると、ログに記録された値がブラウザーの Azure portal に表示されます。

![ポータルでのログに記録された値](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ 
>[!IMPORTANT]
>作成したリソースは、Machine Learning に関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

ここで作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>次の手順

実験してモデルをデプロイするために必要なリソースを作成しました。 ノートブックでコードも実行しました。 クラウドのワークスペースで、そのコードの実行履歴を確認しました。

Machine Learning のチュートリアルでそれを使用するには、他にいくつかのパッケージが環境に必要になります。

1. ブラウザーで、ノートブックを閉じます。
1. コマンド ライン ウィンドウで `Ctrl` + `C` キーを押して、ノートブック サーバーを停止します。
1. 追加パッケージをインストールします。

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

これらのパッケージのインストール後、モデルのトレーニングとデプロイを行うチュートリアルに進みます。 

> [!div class="nextstepaction"]
> [チュートリアル: 画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)

[GitHub でより高度な例](https://aka.ms/aml-notebooks)を確認することもできます。
