---
title: 'クイック スタート: Python SDK を使用して機械学習サービス ワークスペースを作成する - Azure Machine Learning'
description: Azure Machine Learning の利用を開始します。  Python SDK をインストールし、それを使用してワークスペースを作成します。 Azure Machine Learning サービスで機械学習モデルの実験、トレーニング、デプロイを行うとき、このワークスペースがクラウドに置いて基礎ブロックとなります。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067762"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>クイック スタート: Python を使用して Azure Machine Learning の利用を開始する

このクイック スタートでは、Azure Machine Learning SDK for Python を使用して Machine Learning service の[ワークスペース](concept-azure-machine-learning-architecture.md)を作成して使用します。 このワークスペースは、クラウドで Azure Machine Learning service を使用して機械学習モデルの実験、トレーニング、およびデプロイを行うための基礎となるブロックです。

このチュートリアルでは、Python SDK をインストールし、以下を行います。
* Azure サブスクリプションでワークスペースを作成する
* 後で他のノートブックやスクリプトで使用するために、そのワークスペースの構成ファイルを作成する
* ワークスペース内の値をログに記録するためのコードを記述する
* ログに記録した値をワークスペースで表示する

このクイック スタートで作成するワークスペースとその構成ファイルは、Azure Machine Learning に関するその他のチュートリアルとハウツー記事の前提条件として使用することができます。 他の Azure サービスと同様に、Azure Machine Learning service にも、関連付けられている制限とクォータがあります。 クォータの説明と、クォータの引き上げを要求する方法については、[こちら](how-to-manage-quotas.md)を参照してください。

利用者の便宜を図る目的で、リージョンによっては Azure リソースの[コンテナー レジストリ](https://azure.microsoft.com/services/container-registry/)、[ストレージ](https://azure.microsoft.com/services/storage/)、[Application Insights](https://azure.microsoft.com/services/application-insights/)、[キー コンテナー](https://azure.microsoft.com/services/key-vault/)がワークスペースに自動的に追加されます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。


##  <a name="install-the-sdk"></a>SDK のインストール

2018 年 9 月 27 日以降に作成された Data Science Virtual Machine (DSVM) を使用している場合は、それらの DSVM に Python SDK がプリインストールされているため、**このセクションはスキップ**してください。

SDK をインストールする前に、分離された Python 環境を最初に作成することをお勧めします。 このクイック スタートでは [Miniconda](https://conda.io/docs/user-guide/install/index.html) を使用していますが、[Anaconda](https://www.anaconda.com/) をフルインストールしたり、[Python virtualenv](https://virtualenv.pypa.io/en/stable/) を使用したりすることもできます。

### <a name="install-miniconda"></a>Miniconda のインストール


Miniconda を[ダウンロード](https://conda.io/miniconda.html)してインストールします。 Python 3.7 バージョン以降を選択します。 Python 2.x バージョンは選択しないでください。

### <a name="create-an-isolated-python-environment"></a>分離された Python 環境の作成 

コマンド ライン ウィンドウを起動し、Python 3.6 で `myenv` という名前の新しい conda 環境を作成します。

```sh
conda create -n myenv -y Python=3.6
```

環境をアクティブにします。

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>SDK のインストール

アクティブにした conda 環境に SDK をインストールします。 このコードでは、Azure Machine Learning SDK の主要なコンポーネントと Jupyter Notebook サーバーが `myenv` conda 環境にインストールされます。  インストールの完了には**約 4 分**かかります。

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>ワークスペースの作成

このコマンドを入力して、Jupyter Notebook を起動します。
```sh
jupyter notebook
```

ブラウザー ウィンドウで、既定の `Python 3` カーネルを使用して新しいノートブックを作成します。 

ノートブックのセルに次の Python コードを入力して SDK のバージョンを表示し、それを実行します。

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

関連付けられているストレージ、コンテナー レジストリ、キー コンテナーなど、ワークスペースの詳細を表示するには:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>構成ファイルの記述

構成ファイル内のワークスペースの詳細を現在のディレクトリに保存します。 このファイルは、"aml_config\config.json" という名前です。  

このワークスペース構成ファイルを使用すると、後で同じディレクトリ内またはサブディレクトリ内の他のノートブックやスクリプトでこの同じワークスペースを簡単に読み込むことができます。 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

`write_config()` API 呼び出しでは、構成ファイルが現在のディレクトリに作成されます。 `config.json` ファイルには、以下が含まれています。

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

# create a new experiemnt
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
>作成したリソースは、Azure Machine Learning に関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

このクイック スタートで作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>次の手順

モデルの実験とデプロイを開始するために必要なリソースがこれで作成されました。 また、ノートブックでコードを実行し、クラウドのワークスペースでそのコードの実行履歴を確認しました。

Azure Machine Learning のチュートリアルでそれを使用するには、他にいくつかのパッケージが環境に必要になります。

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
