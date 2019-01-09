---
title: Python 開発環境をセットアップする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning サービスで作業する場合の、開発環境を構成する方法について説明します。 このドキュメントでは、Conda 環境の使用、構成ファイルの作成、Jupyter Notebooks、Azure Notebooks、IDE、コード エディター、および Data Science Virtual Machine の構成方法を説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59f847dc38cddfd9185cfd169cf1ef34c744f8f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192636"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のための開発環境を構成する

このドキュメントでは、Azure Machine Learning サービスを操作する開発環境を構成する方法について説明します。 Azure Machine Learning サービスは、プラットフォームに依存しません。 開発環境における要件は、__Python 3__、__Conda__ (分離環境の場合)、構成ファイル (Azure Machine Learning ワークスペース情報を記載) だけです。

このドキュメントでは、次の特定の環境とツールに重点を置いています。

* [Azure Notebooks](#aznotebooks):Azure クラウドでホストされている Jupyter Notebook サービスです。 これは、Azure Machine Learning SDK が既にインストールされているため、作業を開始する __最も簡単な__ 方法です。
* [Data Science Virtual Machine](#dsvm):Azure クラウド内の__事前構成済みの開発/実験環境__です。__データ サイエンスの作業のために設計__されており、CPU のみの VM インスタンスまたは GPU ベースのインスタンスのいずれかにデプロイできます。 Python 3、Conda、Jupyter Notebooks、Azure Machine Learning SDK は既にインストールされています。 VM には、ML ソリューションを開発するための人気の ML/ディープ ラーニング フレームワーク、ツール、エディターが備わっています。 これは、おそらく Azure プラットフォームにおいて ML の __最も包括的な__ 開発環境です。
* [Jupyter Notebook](#jupyter):Jupyter Notebook を既に使用している場合、SDK にはインストールする必要のある追加機能がいくつかあります。
* [Visual Studio Code](#vscode):Visual Studio Code を使用する場合は、いくつかの有用な拡張機能をインストールできます。
* [Azure Databricks](#aml-databricks):Apache Spark がベースの人気のあるデータ分析プラットフォームです。 モデルをデプロイできるように、クラスターに Azure Machine Learning SDK を追加する方法を学習してください。

既に Python 3 環境がある場合、または SDK をインストールするための基本的な手順のみが必要な場合、[ローカル コンピューター](#local)のセクションをご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 [Azure Machine Learning サービスの基本操作](quickstart-get-started.md)に関するページの手順に従って作成します。

- [Continuum Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャーのいずれか。

    > [!IMPORTANT]
    > Azure Notebooks を使用している場合、Anaconda と Miniconda は必要ありません。

- Linux または Mac OS 上では、Bash シェルが必要です。

    > [!TIP]
    > Linux または Mac OS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

- Windows の場合、コマンド プロンプトまたは Anaconda プロンプト (Anaconda および Miniconda によってインストール済み) が必要です。

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (プレビュー) は、Azure クラウドにおける対話型開発環境です。 これは、Azure Machine Learning の開発を開始する __最も簡単な__ 方法です。

* Azure Machine Learning SDK は __既にインストール__ されています。
* Azure portal で Azure Machine Learning サービス ワークスペースを作成した後、ボタンをクリックすると、ワークスペースを操作する Azure Notebook 環境が自動的に構成されます。

Azure Notebooks を使用して開発を開始するには、[Azure Machine Learning サービスの基本操作](quickstart-get-started.md)に関するドキュメントをご覧ください。

## <a id="dsvm"></a>Data Science Virtual Machine

Data Science Virtual Machine (DSVM) は、**データ サイエンスの作業のために設計**された、カスタマイズされた仮想マシン (VM) イメージであり、次のものが事前に構成されています。

  - Tensorflow、Pytorch、scikit-learn、Xgboost、Azure ML SDK などのパッケージ
  - Spark スタンドアロン、Drill などの一般的なデータ サイエンス ツール
  - CLI、Azcopy、Storage Explorer などの Azure ツール
  - Visual Studio Code、PyCharm、RStudio などの統合開発環境 (IDE)
  - Jupyter Notebook Server 

Azure Machine Learning SDK は、DSVM の Ubuntu バージョンまたは Windows バージョンで動作します。 開発環境として Data Science Virtual Machine を使用するには、次の手順を使用します。

1. データ サイエンス仮想マシンを作成するには、次のいずれかの方法を使用します。

    * Azure Portal の使用:

        * [__Ubuntu__ データ サイエンス仮想マシンを作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [__Windows__ データ サイエンス仮想マシンを作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI の使用:

        > [!IMPORTANT]
        > Azure CLI を使用するときは、最初に `az login` コマンドを使用して Azure サブスクリプションにサインインする必要があります。
        >
        > このステップでコマンドを使用するときは、リソース グループ名、VM の名前、ユーザー名、パスワードを指定する必要があります。

        * __Ubuntu__ データ サイエンス仮想マシンを作成するには、次のいずれかのコマンドを使用します。

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * __Windows__ データ サイエンス仮想マシンを作成するには、次のいずれかのコマンドを使用します。

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Azure Machine Learning SDK は DSVM に**既にインストール**されています。 SDK を含む Conda 環境を使用するには、次のいずれかのコマンドを使用します。

    * __Ubuntu__ DSVM では、次のコマンドを使用します。

        ```shell
        conda activate py36
        ```

    * __Windows__ DSVM では、次のコマンドを使用します。

        ```shell
        conda activate AzureML
        ```

1. SDK にアクセスして、バージョンをチェックできることを確認するには、次の Python コードを使用します。

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning サービス ワークスペースを使用するよう DSVM を構成するには、[ワークスペースの構成](#workspace)に関するセクションをご覧ください。

Data Science Virtual Machine の詳細については、[Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)に関するページを参照してください。

## <a id="local"></a>ローカル コンピューター

ローカル コンピューター (リモート仮想マシンの場合もある) を使用する場合は、次の手順を使用して、Conda 環境を作成し、SDK をインストールします。

1. コマンド プロンプトまたはシェルを開きます。

1. 次のコマンドを使用して conda 環境を作成します。

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Python 3.6 やその他のコンポーネントをダウンロードする必要がある場合は、環境を作成するまでに数分かかることがあります。

1. 次のコマンドを使用して、Azure Machine Learning SDK とノートブックのその他の機能および Data Preparation SDK をインストールします。

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > `PyYAML` をアンインストールできないというメッセージが表示された場合は、代わりに次のコマンドを使用してください。
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK をインストールするまでに数分かかることがあります。

1. 機械学習の実験のためのパッケージをインストールします。 次のコマンドを使用し、`<new package>` をインストールするパッケージに置き換えます。

    ```shell
    conda install <new package>
    ```

1. SDK がインストールされていることを確認するには、次の Python コードを使用します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebooks

Jupyter Notebook は、[Jupyter プロジェクト](https://jupyter.org/)の一部です。 これらは、ライブ コードと説明のテキストとグラフィックスが混在するドキュメントを作成する対話型のコーディング エクスペリエンスを提供します。 また、Jupyter Notebook は、ドキュメントにコード セクションの出力を保存できるので、結果を他のユーザーと共有する優れた方法です。 Jupyter Notebook は、さまざまなプラットフォームにインストールできます。

[ローカル コンピューター](#local)のセクションにある手順を実行すると、Jupyter Notebooks のオプションのコンポーネントがインストールされます。 Jupyter Notebook 環境内でこれらのコンポーネントを有効にするには、次の手順を使用します。

1. コマンド プロンプトまたはシェルを開きます。

1. Conda 対応の Jupyter Notebook サーバーをインストールするには、次のコマンドを使用します。

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. 次のコマンドを使用して Jupyter Notebook を開きます。

    ```shell
    jupyter notebook
    ```

1. Jupyter Notebook で SDK を使用できることを確認するには、新しいノートブックを開いて、カーネルとして "myenv" を選択します。 その後、ノートブック セルで次のコマンドを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Azure Machine Learning サービス ワークスペースを使用するよう Jupyter Notebook を構成するには、[ワークスペースの構成](#workspace)に関するセクションをご覧ください。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code はクロス プラットフォーム コード エディターです。 Python サポートについてはローカルの Python 3 および Conda のインストールに依存していますが、AI を操作するための他のツールを提供します。 コード エディター内から Conda 環境を選択するためのサポートも提供します。

開発に Visual Studio Code を使用するには、次の手順を使用します。

1. Python の開発に Visual Studio Code を使用する方法については、[VSCode での Python の使用](https://code.visualstudio.com/docs/python/python-tutorial)に関するドキュメントをご覧ください。

1. Conda 環境を選択するには、VS Code を開いてから、__Ctrl-Shift-P__ (Linux と Windows) または __Command-Shift-P__ (Mac) を使用して __Command Pallet__ を取得します。 「__Python:Select Interpreter__」と入力した後、Conda 環境を選択します。

1. SDK を使用できることを確認するには、次のコードを含む新しい Python ファイル (.py) を作成します。 その後、ファイルを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code 用の Azure Machine Learning 拡張機能をインストールするには、[AI 用のツール](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)に関するページをご覧ください。

    詳しくは、[Visual Studio Code 用の Azure Machine Learning の使用](how-to-vscode-tools.md)に関するページをご覧ください。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

エンド ツー エンドのカスタム機械学習には、Azure Databricks 用の Azure Machine Learning SDK のカスタム バージョンを使用できます。 または、Databricks 内でモデルをトレーニングし、[Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) を使用してモデルをデプロイします

Databricks クラスターを準備し、サンプルのノートブックを取得するには:

1. **Python 3** で Databricks ランタイム バージョン 4.x (高同時実行性推奨) を使用して [Databricks クラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 

1. ライブラリを作成し、Azure Machine Learning SDK for Python `azureml-sdk[databricks]` PyPi パッケージを[インストールしてクラスターにアタッチ](https://docs.databricks.com/user-guide/libraries.html#create-a-library)します。 完了すると、次の図のようにライブラリがアタッチされます。 [Databricks の一般的な問題](resource-known-issues.md#databricks)に注意してください。

   ![Databricks にインストールされた SDK ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   このステップが失敗する場合は、クラスターを再起動します。
   1. 左側のウィンドウで [`Clusters`] を選択します。 テーブルでクラスター名を選択します。 
   1. [`Libraries`] タブで [`Restart`] を選択します。

1. [Azure Databricks/Azure Machine Learning SDK ノートブック アーカイブ ファイル](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc)をダウンロードします。

   >[!Warning]
   > Azure Machine Learning service では多くのサンプル ノートブックを使用できます。 これらのサンプル ノートブックだけが Azure Databricks で動作します。 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  Databricks クラスターに[このアーカイブ ファイルをインポート](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)し、[こちらで説明](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)されているように探索を開始します。


## <a id="workspace"></a>ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning サービス ワークスペースと通信する方法を SDK に示す JSON ドキュメントです。 ファイルの名前は `config.json` で、形式は次のとおりです。

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

このファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内にある必要があります。 同じディレクトリ内、`aml_config` という名前のサブディレクトリ内、または親ディレクトリ内のいずれかにします。

コードからこのファイルを使用するには、`ws=Workspace.from_config()` を使用します。 このコードは、ファイルから情報を読み込み、ワークスペースに接続します。

構成ファイルは 3 つの方法で作成できます。

* [Azure Machine Learning クイック スタート](quickstart-get-started.md)を使用すると、`config.json` ファイルが Azure Notebooks ライブラリ内に作成されます。 このファイルには、ワークスペースの構成情報が含まれています。 この `config.json` を他の開発環境にダウンロードまたはコピーできます。

* テキスト エディターを使用して、**ファイルを手動で作成**できます。 構成ファイルで使用する値を見つけるには、[Azure portal](https://portal.azure.com) のワークスペースにアクセスします。 __ワークスペース名__、__リソース グループ__、__サブスクリプション ID__ の値をコピーし、それらを構成ファイルで使用します。
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **ファイルをプログラムで作成**できます。 次のコード スニペットは、サブスクリプション ID、リソース グループ、ワークスペース名を指定して、ワークスペースに接続する方法を示します。 その後、ワークスペースの構成をファイルに保存します。

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    このコードは、構成ファイルを `aml_config/config.json` ファイルに書き込みます。

## <a name="next-steps"></a>次の手順

- [MNIST データセットを使用して Azure Machine Learning でモデルをトレーニングする](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)
