---
title: Azure Machine Learning の開発環境の構成 | Microsoft Docs
description: Azure Machine Learning サービスで作業する場合の、開発環境を構成する方法について説明します。 このドキュメントでは、Conda 環境の使用、構成ファイルの作成、Jupyter Notebooks、Azure Notebooks、IDE、コード エディター、および Data Science Virtual Machine の構成方法を説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 675dae022376fc62292f3b079bd735939b9199c2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220297"
---
# <a name="configure-a-development-environment-for-the-azure-machine-learning-service"></a>Azure Machine Learning サービスの開発環境の構成

Azure Machine Learning サービスを操作する開発環境を構成する方法について説明します。 環境を Azure Machine Learning ワークスペースに関連付ける構成ファイルを作成する方法を学習します。 次の開発環境を構成する方法も学習します。

* コンピューター上の Jupyter Notebook
* Visual Studio Code
* 任意のコード エディター

推奨されるアプローチとして、パッケージ間の依存関係の競合を防ぐために、Continuum Anaconda の [conda 仮想環境](https://conda.io/docs/user-guide/tasks/manage-environments.html)を使用して作業環境を分離します。 この記事では、conda 環境を設定して、Azure Machine Learning 用に使用するための手順を示します。


## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 作成するには、「[Azure Machine Learning サービスの概要](quickstart-get-started.md)」のドキュメントに記載されている手順に従います。

* [Continuum Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャー。

 * Visual Studio Code 環境の場合、[Python 拡張機能](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="create-workspace-configuration-file"></a>ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、SDK によって Azure Machine Learning サービス ワークスペースと通信するために使用されます。  このファイルを入手するには次の 2 つの方法があります。

* [クイック スタート](quickstart-get-started.md)を完了して、ワークスペースと構成ファイルを作成します。 自動的に `config.json` ファイルが Azure Notebooks に作成されます。  このファイルには、ワークスペースの構成情報が含まれています。  これを参照するスクリプトまたはノートブックと同じディレクトリに、これをダウンロードまたはコピーします。


* 次の手順で構成ファイルを自分で作成します。

    1. [Azure Portal](https://portal.azure.com) でワークスペースを開きます。 __ワークスペース名__、__リソース グループ__、および__サブスクリプション ID__ をコピーします。 これらの値は、構成ファイルの作成に使用されます。

        ![Azure ポータル](./media/how-to-configure-environment/configure.png) 
    
    1. この Python コードを使用してファイルを作成します。 ワークスペースを参照するスクリプトまたはノートブックと同じディレクトリでこのコードを実行します。
        ```
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'
        
        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        これによって、次の `aml_config/config.json` ファイルが書き込まれます。 
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        `aml_config` ディレクトリ、または `config.json` ファイルのみを、ワークスペースを参照する他のディレクトリにコピーできます。

>[!NOTE] 
>同じディレクトリまたは配下のディレクトリにある他のスクリプトやノートブックで、`ws=Workspace.from_config()` を使用してワークスペースを読み込みます。

## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure Notebooks および Data Science Virtual Machine

Azure Notebooks と Azure データ サイエンス仮想マシン (DSVM) は、Azure Machine Learning サービスで動作するように事前構成されています。 Azure Machine Learning SDK などの必要なコンポーネントは、これらの環境にプレインストールされています。

Azure Notebooks は、Azure クラウドの Jupyter Notebook サービスです。 Data Science Virtual Machine は、データ サイエンスの作業向けに事前構成されている VM イメージです。 VM には、一般的なツールや IDE、Jupyter Notebooks、PyCharm、Tensorflow などのパッケージが含まれています。

これらの環境を使用するにはワークスペース構成ファイルが引き続き必要です。

Data Science VM の詳細については、[Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) のドキュメントを参照してください。

Azure Notebooks を Azure Machine Learning サービスで使用する例については、[Azure Machine Learning サービスの概要](quickstart-get-started.md)のドキュメントを参照してください。

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>コンピューターに Jupyter Notebooks を構成する

1. コマンド プロンプトまたはシェルを開きます。

2. conda 環境を作成するには、次のコマンドを使用します。

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Python 3.6 やその他のコンポーネントをダウンロードすることが必要な場合があるため、環境の作成には数分かかることがあります。

3. Azure Machine Learning SDK を Notebook の他の機能とインストールするには、次のコマンドを使用します。

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl]
    ```

    SDK をインストールするには数分かかる場合があります。

4. 機械学習実験用のパッケージをインストールするには、次のコマンドを使用して、`<new package>` をインストールするパッケージで置き換えます。

    ```shell
    conda install <new package>
    ```

5. conda 対応の Jupyter Notebook サーバーをインストールし、(実行情報を表示するために) 実験ウィジェットを有効にするには、次のコマンドを使用します。

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Jupyter Notebook を起動するには、次のコマンドを使用します。

    ```shell
    jupyter notebook
    ```

7. 新しいノートブックを開き、カーネルとして "myenv" を選択します。 Notebook セルで次のコマンドを実行して、Azure Machine Learning SDK がインストールされていることを検証します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Visual Studio Code を構成する

1. コマンド プロンプトまたはシェルを開きます。

2. conda 環境を作成するには、次のコマンドを使用します。

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Azure Machine Learning SDK をインストールするには、次のコマンドを使用します。
 
    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

4. Visual Studio Code の Tools for AI をインストールするには、[Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) の Visual Studio Marketplace のエントリを参照してください。 

5. 機械学習実験用のパッケージをインストールするには、次のコマンドを使用して、`<new package>` をインストールするパッケージで置き換えます。

    ```shell
    conda install <new package>
    ```

6. Visual Studio Code を起動し、__CTRL SHIFT+P__ を使用して__コマンド パレット__を取得します。 *Python: Select Interpreter* を入力し、作成した conda 環境を選択します。

    > [!NOTE]
    > Visual Studio Code は、コンピューター上の conda 環境を自動的に認識します。 詳細については、[Visual Studio Code のドキュメント](https://code.visualstudio.com/docs/python/environments#_conda-environments)を参照してください。

7. 構成を検証するには、Visual Studio Code を使用して、新しい Python スクリプト ファイルを次のコードで作成してそれを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>選択したコード エディターを構成する

Azure Machine Learning SDK でカスタム コード エディターを使用するには、上記の説明に従ってまず conda 環境を作成します。 次に各エディターで手順に従って、conda 環境を使用します。 たとえば、PyCharm の手順は[https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)にあります。
 
## <a name="next-steps"></a>次の手順

* [MNIST データセットを使用して Azure Machine Learning でモデルをトレーニングする](tutorial-train-models-with-aml.md)
