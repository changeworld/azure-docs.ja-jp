---
title: Azure Machine Learning の開発環境の構成 | Microsoft Docs
description: Azure Machine Learning サービスで作業する場合の、開発環境を構成する方法について説明します。 このドキュメントでは、Conda 環境の使用、構成ファイルの作成、Jupyter Notebooks、Azure Notebooks、IDE、コード エディター、および Data Science Virtual Machine の構成方法を説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 10/24/2018
ms.openlocfilehash: 6c2d5a776f603161ef730028168b91844c120aec
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158994"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のための開発環境を構成する

この記事では、Azure Machine Learning サービスを操作するための開発環境を構成する方法について説明します。これには次のものが含まれます。

- 環境を Azure Machine Learning サービス ワークスペースに関連付ける構成ファイルを作成する方法。
- 次の開発環境を構成する方法。
  - コンピューター上の Jupyter Notebook
  - Visual Studio Code
  - カスタム コード エディター
- [conda 仮想環境](https://conda.io/docs/user-guide/tasks/manage-environments.html)を設定し、それを Azure Machine Learning のために使用する方法。 パッケージ間の依存関係の競合を回避するために、Continuum Anaconda を使用して作業環境を分離することをお勧めします。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning サービス ワークスペースを設定してください。 [Azure Machine Learning サービスの基本操作](quickstart-get-started.md)に関するページの手順に従います。
- [Continuum Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://conda.io/miniconda.html) パッケージ マネージャーのどちらかをインストールします。
- Visual Studio Code を使用している場合は、[Python 拡張機能](https://code.visualstudio.com/docs/python/python-tutorial)を入手します。

> [!NOTE]
> この記事に示されているシェル コマンドは、bash (Linux および Mac OS の場合) またはコマンド プロンプト (Windows の場合) を使用してテストできます。

## <a name="create-a-workspace-configuration-file"></a>ワークスペース構成ファイルを作成する

Azure Machine Learning SDK は、ワークスペース構成ファイルを使用して Azure Machine Learning サービス ワークスペースと通信します。

- 構成ファイルを作成するには、[Azure Machine Learning のクイック スタート](quickstart-get-started.md)に関するページを完了します。
  - このクイック スタート プロセスは、Azure Notebooks 内に `config.json` ファイルを作成します。 このファイルには、ワークスペースの構成情報が含まれています。
  - `config.json` を、それを参照するスクリプトまたはノートブックと同じディレクトリにダウンロードまたはコピーします。

- あるいは、次の手順に従うことによって、このファイルを手動で作成できます。

    1. [Azure Portal](https://portal.azure.com) でワークスペースを開きます。 __ワークスペース名__、__リソース グループ__、および__サブスクリプション ID__ をコピーします。 これらの値は、構成ファイルの作成に使用されます。
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. 次の Python コードを含むファイルを作成し、そのコードを、ワークスペースを参照するスクリプトまたはノートブックと同じディレクトリで実行するようにします。

        ```python
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
        このコードによって、次の `aml_config/config.json` ファイルが書き込まれます。

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        `aml_config` ディレクトリ、または `config.json` ファイルのみを、ワークスペースを参照する他のディレクトリにコピーできます。

       > [!NOTE]
       > 同じディレクトリかまたはその下のディレクトリにある他のスクリプトまたはノートブックが `ws=Workspace.from_config()` を使用してワークスペースを読み込みます。

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks および Data Science Virtual Machine

Azure Notebooks および Azure Data Science Virtual Machine (DSVM) は、Azure Machine Learning サービスを操作するように構成されて出荷されます。 これらの環境には、Azure Machine Learning SDK などの必要なコンポーネントが含まれています。

- Azure Notebooks は、Azure クラウドの Jupyter Notebook サービスです。
- Data Science Virtual Machine は、データ サイエンスの作業のために設計された、カスタマイズされた仮想マシン (VM) イメージです。 次の情報が含まれます。
  - 一般的なツール
  - 統合開発環境 (IDE)
  - Jupyter Notebook、PyCharm、Tensorflow などのパッケージ
- これらの環境を使用するには、ワークスペース構成ファイルが引き続き必要です。

Azure Machine Learning サービスで Azure Notebooks を使用する例については、[Azure Machine Learning サービスの基本操作](quickstart-get-started.md)に関するページを参照してください。

Data Science Virtual Machine の詳細については、[Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)に関するページを参照してください。

## <a name="configure-jupyter-notebooks-on-your-computer"></a>コンピューター上で Jupyter Notebook を構成する

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

   以下の SDK では、クラスとメソッドの Python リファレンス ドキュメントを参照することができます。
   + [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk)
   + [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > `PyYAML` をアンインストールできないというメッセージが表示された場合は、代わりに次のコマンドを使用してください。
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK をインストールするまでに数分かかることがあります。

1. 機械学習の実験のためのパッケージをインストールします。 次のコマンドを使用し、`<new package>` をインストールするパッケージに置き換えます。

    ```shell
    conda install <new package>
    ```

1. conda 対応の Jupyter Notebook サーバーをインストールし、実験ウィジェットを有効にします (実行情報を表示するため)。 次のコマンドを使用します。

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. 次のコマンドを使用して Jupyter Notebook を開きます。

    ```shell
    jupyter notebook
    ```

1. 新しいノートブックを開き、カーネルとして "myenv" を選択してから、Azure Machine Learning SDK がインストールされたことを検証します。 ノートブック セルで次のコマンドを実行します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Visual Studio Code を構成する

1. コマンド プロンプトまたはシェルを開きます。

1. 次のコマンドを使用して conda 環境を作成します。

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. 次のコマンドを使用して、Azure Machine Learning SDK および Data Preparation SDK をインストールします。

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Visual Studio Code Tools for AI 拡張機能をインストールします。 [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) に関するページを参照してください。

1. 機械学習の実験のためのパッケージをインストールします。 次のコマンドを使用し、`<new package>` をインストールするパッケージに置き換えます。

    ```shell
    conda install <new package>
    ```

1. Visual Studio Code を開き、**CTRL-SHIFT-P** (Windows の場合) または **COMMAND-SHIFT-P** (Mac OS の場合) を使用して**コマンド パレット**を取得します。 「_Python: Select Interpreter_」と入力し、作成した conda 環境を選択します。

   > [!NOTE]
   > Visual Studio Code は、コンピューター上の conda 環境を自動的に認識します。 詳細については、[Visual Studio Code のドキュメント](https://code.visualstudio.com/docs/python/environments#_conda-environments)を参照してください。

1. Visual Studio Code を使用して、次のコードで新しい Python スクリプト ファイルを作成し、それを実行することによって構成を検証します。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>カスタム コード エディターを構成する

Azure Machine Learning SDK では、任意のコード エディターを使用できます。

1. 上の「[Visual Studio Code を構成する](#configure-visual-studio-code)」の手順 2 の説明に従って、conda 環境を作成します。
1. 各エディターの手順に従って、conda 環境を使用します。 たとえば、[PyCharm の手順](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)に従うことができます。

## <a name="next-steps"></a>次の手順

- [MNIST データセットを使用して Azure Machine Learning でモデルをトレーニングする](tutorial-train-models-with-aml.md)
