---
title: クイック スタート:Python で作業開始
titleSuffix: Azure Machine Learning service
description: Python で Azure Machine Learning service の作業を開始します。 Python SDK を使用して、機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなるワークスペースを作成します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab55cec4b8483cf254ec3d9fe68521baca9cdf5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268514"
---
# <a name="quickstart-use-python-sdk-to-get-started-with-azure-machine-learning"></a>クイック スタート:Python SDK を使用して Azure Machine Learning の利用を開始する

このクイック スタートでは、Azure Machine Learning SDK for Python を使用して Machine Learning サービスの[ワークスペース](concept-azure-machine-learning-architecture.md)を作成して使用します。 このワークスペースが、Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。 このクイック スタートでは、まず独自の Python 環境と Jupyter Notebook サーバーを構成します。 インストールなしで実行するには、「[クイック スタート: Azure portal を使用した Azure Machine Learning の基本操作](quickstart-get-started.md)」を参照してください。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

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

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="install-the-sdk"></a>SDK のインストール

>[!NOTE]
> この記事のコードには、Azure Machine Learning SDK バージョン 1.0.2 以降が必要です。 

*2018 年 9 月 27 日より後に作成されたデータ サイエンス仮想マシンを使用する場合は、このセクションをスキップします。* これらのデータ サイエンス仮想マシンには、Python SDK がプレインストールされています。

SDK をインストールする前に、分離された Python 環境を作成することをお勧めします。 このクイック スタートでは [Miniconda](https://conda.io/docs/user-guide/install/index.html) を使用していますが、フル インストールした [Anaconda](https://www.anaconda.com/) や [Python virtualenv](https://virtualenv.pypa.io/en/stable/) を使用することもできます。

### <a name="install-miniconda"></a>Miniconda のインストール


Miniconda を[ダウンロード](https://conda.io/miniconda.html)してインストールします。 Python 3.7 以降のバージョンを選択します。 Python 2.x バージョンは選択しないでください。

### <a name="create-an-isolated-python-environment"></a>分離された Python 環境の作成 

コマンド ライン ウィンドウを開きます。 次に、Python 3.6 で `myenv` という名前の新しい conda 環境を作成します。

```shell
conda create -n myenv -y Python=3.6
```

環境をアクティブにします。

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>SDK のインストール

アクティブにした conda 環境に SDK をインストールします。 このコードによって、Machine Learning SDK の主要なコンポーネントがインストールされます。 conda 環境に Jupyter Notebook サーバーもインストールされます。 お使いのマシンの構成によっては、インストールが完了するまでに数分かかります。

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

別途、各種のキーワードを使用して、追加の SDK コンポーネントをインストールすることもできます。

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Databricks 環境では、このインストールを代わりに使用します。

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>ワークスペースの作成

Jupyter Notebook を起動するには、このコマンドを入力します。
```shell
jupyter notebook
```

ブラウザー ウィンドウで、既定の `Python 3` カーネルを使用して新しいノートブックを作成します。 

SDK のバージョンを表示するには、ノートブックのセルに次の Python コードを入力し、それを実行します。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>構成ファイルの記述

構成ファイル内のワークスペースの詳細を現在のディレクトリに保存します。 このファイルは、"aml_config\config.json" という名前です。  

このワークスペース構成ファイルを使用すると、後でこの同じワークスペースを簡単に読み込むことができます。 同じディレクトリまたはサブディレクトリ内の他のノートブックやスクリプトと共に読み込むことができます。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


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

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>次の手順

実験してモデルをデプロイするために必要なリソースを作成しました。 ノートブックでコードも実行しました。 クラウドのワークスペースで、そのコードの実行履歴を確認しました。

Machine Learning のチュートリアルでそれを使用するには、他にいくつかのパッケージが環境に必要になります。

1. ブラウザーで、ノートブックを閉じます。
1. コマンド ライン ウィンドウで `Ctrl` + `C` キーを押して、ノートブック サーバーを停止します。
1. 追加パッケージをインストールします。

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


これらのパッケージのインストール後、モデルのトレーニングとデプロイを行うチュートリアルに進みます。 

> [!div class="nextstepaction"]
> [チュートリアル:画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)

[GitHub でより高度な例](https://aka.ms/aml-notebooks)を確認することもできます。
