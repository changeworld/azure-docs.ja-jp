---
title: 開発環境をセットアップする | Python
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のために Python 開発環境をセットアップすることについて説明します。 Conda 環境を使用し、構成ファイルを作成し、独自のクラウドベースのノートブック サーバー、Jupyter Notebook、Azure Databricks、IDE、コード エディター、および Data Science Virtual Machine を構成します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 98bc108af22491c6ea0b64bc2e278b6b32f43a5b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203094"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning のために開発環境をセットアップする

Azure Machine Learning のために Python 開発環境を構成する方法について説明します。

次の表では、この記事で説明されている各開発環境と、その長所および短所を示します。

| 環境 | 長所 | 短所 |
| --- | --- | --- |
| [ローカル環境](#local) | 開発環境と依存関係を完全に制御できます。 任意のビルド ツール、環境、または IDE を使用できます。 | 始めるのに時間がかかります。 必要な SDK パッケージをインストールする必要があり、環境がまだない場合はそれもインストールする必要があります。 |
| [Azure Machine Learning コンピューティング インスタンス](#compute-instance) | 最も簡単に始められる方法です。 SDK 全体は既にワークスペース VM にインストールされており、ノートブックのチュートリアルは事前に複製され、実行準備ができています。 | 開発環境と依存関係を制御することはできません。 Linux VM では追加コストが発生します (VM を使用していない場合は料金を回避するために停止できます)。 [価格の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 |
| [Azure Databricks](#aml-databricks) | スケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に適しています。 | 実験用機械学習、または小規模な実験とワークフローでは過剰です。 Azure Databricks の追加コストが発生します。 [価格の詳細](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | クラウドベースのコンピューティング インスタンスに似ていますが (Python と SDK はプレインストールされています)、その他の一般的なデータ サイエンスおよび機械学習ツールがプレインストールされています。 簡単にスケーリングし、他のカスタム ツールやワークフローと組み合わせることができます。 | クラウドベースのコンピューティング インスタンスと比較して、作業の開始に時間がかかります。 |

この記事では、次のツールに関する追加の使用ヒントについても説明します。

* Jupyter Notebook: Jupyter Notebooks を既に使用している場合、SDK にはインストールすべきいくつかの追加機能があります。

* Visual Studio Code:Visual Studio Code を使用する場合、[Azure Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)には、Python の幅広い言語サポートに加えて、Azure Machine Learning を使いやすく、生産性を高めるための機能が含まれています。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 これがない場合は、[Azure portal](how-to-manage-workspace.md)、[Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)を使用して、Azure Machine Learning ワークスペースを作成できます。

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (ローカルおよび DSVM のみ) ワークスペース構成ファイルを作成する

ワークスペース構成ファイルは、Azure Machine Learning ワークスペースと通信する方法を SDK に示す JSON ファイルです。 ファイルの名前は *config.json* で、形式は次のとおりです。

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

この JSON ファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内にある必要があります。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。

コードからこのファイルを使用するには、[`Workspace.from_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) メソッドを使用します。 このコードは、ファイルから情報を読み込み、ワークスペースに接続します。

次のいずれかの方法で、ワークスペース構成ファイルを作成します。

* Azure portal

    **ファイルをダウンロードする**:[Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。

    ![Azure portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Azure Machine Learning ワークスペースに接続するためのスクリプトを作成し、[`write_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) メソッドを使用してファイルを生成し、それを *.azureml/config.json* として保存します。 必ず `subscription_id`、`resource_group`、`workspace_name` を独自の値に置き換えてください。

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

## <a name="local-computer"></a><a id="local"></a>ローカル コンピューター

ローカル開発環境 (Azure Machine Learning コンピューティング インスタンスや DSVM などのリモート仮想マシンである場合もあります) を構成するには、次のようにします。

1. Python 仮想環境を作成します (virtualenv、conda)。

    > [!NOTE]
    > 必須ではありませんが、Python 仮想環境の管理とパッケージのインストールのためには、[Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://www.anaconda.com/download/) を使用することが推奨されます。

    > [!IMPORTANT]
    > Linux または macOS 上で Bash 以外のシェル (例: zsh) を使用している場合、一部のコマンドを実行するとエラーが発生する可能性があります。 この問題を回避するには、`bash` コマンドを使用して新しい Bash シェルを開始し、そこでコマンドを実行します。

1. 新しく作成された Python 仮想環境をアクティブにします。
1. [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) をインストールします。
1. Azure Machine Learning ワークスペースを使用するようにローカル環境を構成するには、[ワークスペース構成ファイルを作成する](#workspace)か、既存のものを使用します。

ローカル環境をセットアップしたので、Azure Machine Learning の操作を開始する準備ができました。 使用を開始するには、[Azure Machine Learning Python のファースト ステップ ガイド](tutorial-1st-experiment-sdk-setup-local.md) を参照してください。

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

ローカル Jupyter Notebook サーバーを実行する場合は、Python 仮想環境用の IPython カーネルを作成することが推奨されます。 これは、予期されるカーネルおよびパッケージのインポート動作が確実に行われるようにするのに役立ちます。

1. 環境固有の IPython カーネルを有効にします

    ```bash
    conda install notebook ipykernel
    ```

1. Python 仮想環境用のカーネルを作成します。 `<myenv>` は必ず、Python 仮想環境の名前に置き換えてください。

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Jupyter Notebook を起動します

Azure Machine Learning と Jupyter Notebook の使用を開始するには、[Azure Machine Learning ノートブックのリポジトリ](https://github.com/Azure/MachineLearningNotebooks)を参照してください。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

開発に Visual Studio Code を使用するには

1. [Visual Studio Code](https://code.visualstudio.com/Download) をインストールします。
1. [Visual Studio Code の Azure Machine Learning 拡張機能](tutorial-setup-vscode-extension.md) (プレビュー) をインストールします。

Visual Studio Code の拡張機能をインストールしたら、[Azure Machine Learning リソース](how-to-manage-resources-vscode.md)の管理、[実験の実行とデバッグ](how-to-debug-visual-studio-code.md)、[トレーニング済みモデルのデプロイ](tutorial-train-deploy-image-classification-model-vscode.md)を行うことができます。

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning コンピューティング インスタンス

Azure Machine Learning の[コンピューティング インスタンス](concept-compute-instance.md)は、セキュリティで保護された、クラウドベースの Azure ワークステーションであり、データ サイエンティストに、Jupyter Notebook サーバー、JupyterLab、フル マネージドの機械学習環境を提供します。

コンピューティング インスタンスでインストールまたは構成するものはありません。  

Azure Machine Learning ワークスペース内から、いつでも作成できます。 名前を指定し、Azure VM の種類を指定するだけです。 今すぐお試しいただくには、「[チュートリアル:環境とワークスペースの設定に関するチュートリアル](tutorial-1st-experiment-sdk-setup.md)を参照)。

パッケージのインストール方法など、コンピューティング インスタンスの詳細については、[コンピューティング インスタンス](concept-compute-instance.md)に関する記事を参照してください。

> [!TIP]
> 未使用のコンピューティング インスタンスに対する課金が発生しないようにするには、[コンピューティング インスタンス](tutorial-1st-experiment-bring-data.md#clean-up-resources)を停止します。

コンピューティング インスタンスは、Jupyter Notebook サーバーと JupyterLab に加えて、[Azure Machine Learning スタジオの内部に統合されたノートブック機能](how-to-run-jupyter-notebooks.md)で使用できます。

また、Visual Studio Code の Azure Machine Learning 拡張機能を使用して、[Azure Machine Learning コンピューティング インスタンスをリモート Jupyter Notebook サーバー](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server)として構成することもできます。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

DSVM は、カスタマイズされた仮想マシン (VM) イメージです。 これはデータ サイエンス作業向けに設計されていて、以下のようなツールとソフトウェアが事前に構成されています。

  - TensorFlow、PyTorch、Scikit-learn、XGBoost、Azure Machine Learning SDK などのパッケージ
  - Spark スタンドアロンや Drill などの一般的なデータ サイエンス ツール
  - Azure CLI、AzCopy、Storage Explorer などの Azure ツール
  - Visual Studio Code や PyCharm などの統合開発環境 (IDE)
  - Jupyter Notebook Server

ツールの包括的な一覧については、[DSVM に付属するツールのガイド](data-science-virtual-machine/tools-included.md)を参照してください。

> [!IMPORTANT]
> DSVM をトレーニングまたは推論ジョブのための[コンピューティング先](concept-compute-target.md)として使用する予定の場合は、Ubuntu のみがサポートされます。

DSVM を開発環境として使用するには

1. 以下のいずれかの方法を使用して DSVM を作成します。

    * Azure portal を使用して、[Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) または [Windows](data-science-virtual-machine/provision-vm.md) DSVM を作成します。
    * [ARM テンプレートを使用して DSVM を作成します](data-science-virtual-machine/dsvm-tutorial-resource-manager.md)。
    * Azure CLI の使用

        Ubuntu DSVM を作成するには、次のコマンドを使用します。

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Windows DSVM を作成するには、次のコマンドを使用します。

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Azure Machine Learning SDK を含む conda 環境をアクティブにします。

    * Ubuntu DSVM の場合:

        ```bash
        conda activate py36
        ```

    * Windows DSVM の場合:

        ```bash
        conda activate AzureML
        ```

1. Azure Machine Learning ワークスペースを使用するよう DSVM を構成するには、[ワークスペース構成ファイルを作成する](#workspace)か、既存のものを使用します。

ローカル環境と同様に、Visual Studio Code と、[Visual Studio Code の Azure Machine Learning 拡張機能](#vscode)を使用して、Azure Machine Learning を操作できます。

詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、CPU または GPU ベースのコンピューティング クラスターにより、コラボレーションに適した Notebook ベースの環境を提供するものです。

Azure Databricks が Azure Machine Learning と連携する仕組み:

+ Spark MLlib を使用してモデルをトレーニングし、そのモデルを Azure Databricks 内から ACI/AKS にデプロイできます。
+ また、Azure Databricks を使用して、特別な Azure ML SDK で[自動化された機械学習](concept-automated-ml.md)機能を使用することもできます。
+ Azure Databricks は、[Azure Machine Learning パイプライン](concept-ml-pipelines.md)からのコンピューティング先として使用できます。

### <a name="set-up-your-databricks-cluster"></a>Databricks クラスターを設定する

[Databricks クラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 一部の設定は、Databricks に自動化された機械学習用の SDK をインストールする場合にのみ適用されます。
**クラスターの作成には数分かかります。**

次の設定を使用します。

| 設定 |適用対象| 値 |
|----|---|---|
| クラスター名 |常時| yourclustername |
| Databricks ランタイム |常時|ML 以外のランタイム 7.1 (scala 2.21、spark 3.0.0) |
| Python バージョン |常時| 3 |
| ワーカー |常時| 2 以上 |
| ワーカー ノードの VM の種類 <br>(同時実行反復処理の最大数を決定) |自動化された ML<br>のみ| メモリ最適化 VM 優先 |
| 自動スケールの有効化 |自動化された ML<br>のみ| オフ |

クラスターが実行中になるまで待機してから、次に進んでください。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Databricks ライブラリに適切な SDK をインストールする

クラスターが実行されたら、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)して、適切な Azure Machine Learning SDK パッケージをクラスターにアタッチします。 自動 ML については、[「自動機械学習を含む Databricks 用 SDK」セクション](#sdk-for-databricks-with-automated-machine-learning)を参照してください。

1. ライブラリを保存する現在のワークスペース フォルダーを右クリックします。 **[作成]**  >  **[ライブラリ]** の順に選択します。

1. 次のオプションを選択します (他の SDK インストールはサポート対象外)

   |SDK&nbsp;パッケージ&nbsp;extras|source|PyPi&nbsp;名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks 用| Python Egg または PyPI のアップロード | azureml-sdk[databricks]|

   > [!Warning]
   > 他の SDK extras はインストールできません。 [`databricks`] オプションのみを選択します。

   * **[すべてのクラスターに自動的にアタッチする]** は選択しないでください。
   * クラスター名の横にある **[アタッチ]** を選択します。

1. 状態が **[アタッチ済み]** に変わるまで、エラーを監視します。これには数分かかる場合があります。  このステップが失敗した場合は、次の操作を行います。

   次のようにして、クラスターを再起動してみます。
   1. 左側のウィンドウで、 **[クラスター]** を選択します。
   1. テーブルでクラスター名を選択します。
   1. **[ライブラリ]** タブで **[再起動]** を選択します。

   次の点も考慮します。
   + AutoML 構成では、Azure Databricks を使用するときに次のパラメーターを追加してください。
       1. ```max_concurrent_iterations``` は、ご利用のクラスター内のワーカー ノードの数に基づきます。
        2. ```spark_context=sc``` は、既定の Spark コンテキストに基づきます。
   + または、古いバージョンの SDK がある場合は、クラスターのインストール済みライブラリでその SDK の選択を解除し、ごみ箱に移動します。 新しいバージョンの SDK をインストールし、クラスターを再起動します。 再起動後に問題がある場合は、クラスターをデタッチし、再アタッチします。

インストールが成功した場合、インポートされたライブラリは次のどちらかのような外観になります。

#### <a name="sdk-for-databricks"></a>Databricks 用 SDK
![Databricks 用の Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

#### <a name="sdk-for-databricks-with-automated-machine-learning"></a>自動機械学習を含む Databricks 用 SDK
クラスターが ML 以外の Databricks ランタイム 7.1 以降で作成された場合は、ノートブックの最初のセルで次のコマンドを実行して AML SDK をインストールします。

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
ML 以外の Databricks ラインタイム 7.0 以前の場合は、[init スクリプト](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)を使用して AML SDK をインストールします。


### <a name="start-exploring"></a>実際に使ってみる

実際に試してみましょう。
+ サンプル ノートブックがたくさんありますが、**Azure Databricks で動作するのは[これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)のみです。**

+ これらのサンプルは、ワークスペースから直接インポートしてください。 次を参照してください。![インポートの選択](./media/how-to-configure-environment/azure-db-screenshot.png)
![パネルのインポ―ト](./media/how-to-configure-environment/azure-db-import.png)

+ [トレーニング コンピューティングとして Databricks を使用してパイプラインを作成](how-to-create-your-first-pipeline.md)する方法を確認します。

## <a name="next-steps"></a>次のステップ

- MNIST データセットを使用して Azure Machine Learning 上で[モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) リファレンスを表示する
