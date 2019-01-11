---
title: Python 開発環境をセットアップする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で作業する場合の、開発環境を構成する方法について説明します。 この記事では、Conda 環境の使用、構成ファイルの作成、Jupyter Notebooks、Azure Notebooks、IDE、コード エディター、および Data Science Virtual Machine の構成方法を説明します。
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
ms.openlocfilehash: 46a1872d2ac5d1670620148edf7ee273580826d3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811275"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のための開発環境を構成する

この記事では、Azure Machine Learning service を操作する開発環境を構成する方法について説明します。 Azure Machine Learning service は、プラットフォームに依存しません。 

開発環境における要件は、Python 3、Conda (分離環境の場合)、構成ファイル (Azure Machine Learning ワークスペース情報を記載) だけです。

この記事では、次の環境とツールに重点を置いています。

* [Azure Notebooks](#aznotebooks):Azure クラウドでホストされている Jupyter Notebook サービスです。 これは、Azure Machine Learning SDK が既にインストールされているため、作業を開始する最も簡単な方法です。

* [Data Science Virtual Machine (DSVM)](#dsvm):Azure クラウド内の事前構成済みの開発または実験環境です。データ サイエンスの作業のために設計されており、CPU のみの VM インスタンスまたは GPU ベースのインスタンスのいずれかにデプロイできます。 Python 3、Conda、Jupyter Notebooks、Azure Machine Learning SDK は既にインストールされています。 VM には、機械学習ソリューションを開発するための人気の機械学習およびディープ ラーニングのフレームワーク、ツール、エディターが備わっています。 これは、おそらく Azure プラットフォームにおいて機械学習用の最も包括的な開発環境です。

* [Jupyter Notebook](#jupyter):Jupyter Notebook を既に使用している場合、SDK にはインストールする必要のある追加機能がいくつかあります。

* [Visual Studio Code](#vscode):Visual Studio Code を使用する場合は、いくつかの有用な拡張機能をインストールできます。

* [Azure Databricks](#aml-databricks):Apache Spark がベースの人気のあるデータ分析プラットフォームです。 モデルをデプロイできるように、クラスターに Azure Machine Learning SDK を追加する方法を学習してください。

既に Python 3 環境がある場合、または SDK をインストールするための基本的な手順のみが必要な場合、[ローカル コンピューター](#local)のセクションをご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning service の基本操作](quickstart-get-started.md)に関するページを参照してください。

- [Continuum Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャーのいずれか。

    > [!IMPORTANT]
    > Azure Notebooks を使用している場合、Anaconda と Miniconda は必要ありません。

- Linux または macOS 上では、Bash シェルが必要です。

    > [!TIP]
    > Linux または macOS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

- Windows の場合、コマンド プロンプトまたは Anaconda プロンプト (Anaconda および Miniconda によってインストール済み) が必要です。

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (プレビュー) は、Azure クラウドにおける対話型開発環境です。 これは、Azure Machine Learning の開発を開始する最も簡単な方法です。

* Azure Machine Learning SDK は既にインストールされています。
* Azure portal で Azure Machine Learning service ワークスペースを作成した後、ボタンをクリックすると、ワークスペースを操作する Azure Notebook 環境が自動的に構成されます。

Azure Notebooks を使用して開発を開始するには、[Azure Machine Learning service の基本操作](quickstart-get-started.md)に関するページを参照してください。

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM は、カスタマイズされた仮想マシン (VM) イメージです。 これは、以下の項目が事前に構成されているデータ サイエンスの作業向けに設計されています。

  - TensorFlow、PyTorch、Scikit-learn、XGBoost、Azure Machine Learning SDK などのパッケージ。
  - Spark スタンドアロンや Drill などの一般的なデータ サイエンス ツール。
  - Azure CLI、AzCopy、Storage Explorer などの Azure ツール。
  - Visual Studio Code、PyCharm、RStudio などの統合開発環境 (IDE)。
  - Jupyter Notebook Server。 

Azure Machine Learning SDK は、DSVM の Ubuntu バージョンまたは Windows バージョンで動作します。 DSVM を開発環境として使用するには、以下の手順を実行します。

1. 次のいずれかの環境で、DSVM を作成します。

    * Microsoft Azure portal:

        * [Ubuntu Data Science Virtual Machine を作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine を作成する](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Azure CLI を使用するときは、最初に `az login` コマンドを使用して Azure サブスクリプションにサインインする必要があります。
        >
        > * この手順でコマンドを使用するときは、リソース グループ名、VM の名前、ユーザー名、パスワードを指定する必要があります。

        * Ubuntu Data Science Virtual Machine を作成するには、次のいずれかのコマンドを使用します。

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine を作成するには、次のいずれかのコマンドを使用します。

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Azure Machine Learning SDK は DSVM に既にインストールされています。 SDK を含む Conda 環境を使用するには、次のいずれかのコマンドを使用します。

    * Ubuntu DSVM の場合:

        ```shell
        conda activate py36
        ```

    * Windows DSVM の場合:

        ```shell
        conda activate AzureML
        ```

1. SDK にアクセスして、バージョンをチェックできることを確認するには、次の Python コードを使用します。

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning service ワークスペースを使用するよう DSVM を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。

詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。

## <a id="local"></a>ローカル コンピューター

ローカル コンピューター (リモート仮想マシンの場合もある) を使用している場合は、以下の手順を実行して、Conda 環境を作成し、SDK をインストールします。

1. コマンド プロンプトまたはシェルを開きます。

1. 次のコマンドを使用して Conda 環境を作成します。

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Python 3.6 やその他のコンポーネントをダウンロードする必要がある場合は、環境を作成するまでに数分かかることがあります。

1. 次のコマンドを使用して、Azure Machine Learning SDK とノートブックのその他の機能および Data Preparation SDK をインストールします。

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > PyYAML をアンインストールできないというメッセージが表示された場合は、代わりに次のコマンドを使用してください。
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK をインストールするまでに数分かかることがあります。

1. 機械学習の実験のためのパッケージをインストールします。 次のコマンドを使用し、*\<新パッケージ>* を、インストールするパッケージに置き換えます。

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

[ローカル コンピューター](#local)のセクションにある手順を実行すると、Jupyter Notebooks のオプションのコンポーネントがインストールされます。 Jupyter Notebook 環境内でこれらのコンポーネントを有効にするには、以下の手順を実行します。

1. コマンド プロンプトまたはシェルを開きます。

1. Conda 対応の Jupyter Notebook Server をインストールするには、次のコマンドを使用します。

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. 次のコマンドを使用して Jupyter Notebook を開きます。

    ```shell
    jupyter notebook
    ```

1. Jupyter Notebook で SDK を使用できることを確認するには、新しいノートブックを開いて、カーネルとして **myenv** を選択し、ノートブック セルで次のコマンドを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Azure Machine Learning service ワークスペースを使用するよう Jupyter Notebook を構成するには、「[ワークスペース構成ファイルを作成する](#workspace)」セクションを参照してください。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code はクロス プラットフォーム コード エディターです。 Python サポートについてはローカルの Python 3 および Conda のインストールに依存していますが、AI を操作するための他のツールを提供します。 コード エディター内から Conda 環境を選択するためのサポートも提供します。

開発に Visual Studio Code を使用するには、以下の手順を実行します。

1. Python の開発に Visual Studio Code を使用する方法については、[VSCode での Python の使用](https://code.visualstudio.com/docs/python/python-tutorial)に関するページを参照してください。

1. Conda 環境を選択するには、VS Code を開いてから、Ctrl+Shift+P (Linux と Windows) または Command+Shift+P (Mac) を選択します。  
    __Command Pallet__ が開きます。 

1. 「__Python:Select Interpreter__」と入力した後、Conda 環境を選択します。

1. SDK を使用できることを確認するには、次のコードを含む新しい Python ファイル (.py) を作成してから実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code 用の Azure Machine Learning 拡張機能をインストールするには、[AI 用のツール](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)に関するページを参照してください。

    詳細については、[Visual Studio Code 用の Azure Machine Learning の使用](how-to-vscode-tools.md)に関するページを参照してください。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

エンド ツー エンドのカスタム機械学習には、Azure Databricks 用の Azure Machine Learning SDK のカスタム バージョンを使用できます。 または、Databricks 内でモデルをトレーニングし、[Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) を使用してモデルをデプロイできます。

Databricks クラスターを準備し、サンプルのノートブックを取得するには:

1. Python 3 で Databricks ランタイム バージョン 4.x (高同時実行性推奨) を使用して [Databricks クラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 

1. Azure Machine Learning SDK for Python `azureml-sdk[databricks]` PyPi パッケージをインストールしてクラスターにアタッチするには、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)します。  
    完了すると、ライブラリは、次の図に示すようにアタッチされます。 [Databricks の一般的な問題](resource-known-issues.md#databricks)に注意してください。

   ![Databricks にインストールされた SDK ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   この手順が失敗する場合は、以下の手順を実行してクラスターを再起動します。

   a. 左側のウィンドウで、**[クラスター]** を選択します。 
   
   b. テーブルでクラスター名を選択します。 

   c. **[ライブラリ]** タブで **[再起動]** を選択します。

1. [Azure Databricks/Azure Machine Learning SDK ノートブック アーカイブ ファイル](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc)をダウンロードします。

   >[!Warning]
   > Azure Machine Learning service では多くのサンプル ノートブックを使用できます。 [これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)だけが Azure Databricks で動作します。
   > 

1.  Databricks クラスターに[このアーカイブ ファイルをインポート](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)し、[Machine Learning のノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)に関するページで説明されているように探索を開始します。


## <a id="workspace"></a>ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning service ワークスペースと通信する方法を SDK に示す JSON ファイルです。 ファイルの名前は *config.json* で、形式は次のとおりです。

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

この JSON ファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内にある必要があります。 同じディレクトリ内、*aml_config* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。

コードからこのファイルを使用するには、`ws=Workspace.from_config()` を使用します。 このコードは、ファイルから情報を読み込み、ワークスペースに接続します。

構成ファイルは 3 つの方法で作成できます。

* **[Azure Machine Learning のクイックスタート](quickstart-get-started.md)に関するページに従う**:*config.json* ファイルが Azure Notebooks ライブラリ内に作成されます。 このファイルには、ワークスペースの構成情報が含まれています。 *config.json* を他の開発環境にダウンロードまたはコピーできます。

* **ファイルを手動で作成する**:この方法では、テキスト エディターを使用します。 構成ファイルに入力される値を見つけるには、[Azure portal](https://portal.azure.com) のワークスペースにアクセスします。 ワークスペース名、リソース グループ、サブスクリプション ID の値をコピーし、それらを構成ファイルで使用します。

     ![Azure ポータル](./media/how-to-configure-environment/configure.png)

* **ファイルをプログラムで作成する**:次のコード スニペットでは、サブスクリプション ID、リソース グループ、ワークスペース名を指定して、ワークスペースに接続します。 その後、ワークスペースの構成をファイルに保存します。

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

    このコードは、構成ファイルを *aml_config/config.json* ファイルに書き込みます。

## <a name="next-steps"></a>次の手順

- [MNIST データセットを使用して Azure Machine Learning でモデルをトレーニングする](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)
