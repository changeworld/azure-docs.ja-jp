---
title: VS Code からモデルをトレーニングおよびデプロイする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning for Visual Studio Code について、および Visual Studio Code を使用して Azure Machine Learning サービスで機械学習およびディープ ラーニング モデルのトレーニングとデプロイを開始する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0910cce92c410a68dce6e2c44d29e72e594cd153
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271710"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Visual Studio Code を使用して機械学習モデルをトレーニングおよびデプロイする

この記事では、**Azure Machine Learning for Visual Studio Code** 拡張機能を使用して、Visual Studio Code (VS Code) で Azure Machine Learning サービスを使用して機械学習およびディープ ラーニング モデルをトレーニングおよびデプロイする方法について説明します。

Azure Machine Learning は、実験をローカルおよびリモート コンピューティング ターゲットで実行するためのサポートを提供します。 どの実験についても、必要な頻度で複数の実行を追跡し、異なる手法やハイパーパラメーターなどを反復して試することができます。 Azure Machine Learning を使用してカスタム メトリックや実験の実行を追跡し、データ サイエンスの再現性と監査機能を実現できます。

また、これらのモデルをテストや運用のニーズのために展開できます。

## <a name="prerequisites"></a>前提条件

+ Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning サービス](http://aka.ms/AMLFree)を今日からお試しいただけます。

+ [Azure Machine Learning for VS Code](how-to-vscode-tools.md) 拡張機能が設定されている。

+ VS Code を使用して [Python 用の Azure Machine Learning SDK がインストールされている](how-to-vscode-tools.md)。

## <a name="create-and-manage-compute-targets"></a>コンピューティング ターゲットを作成して管理する

Azure Machine Learning for VS Code を使用すると、データを準備し、モデルをトレーニングして、それらをローカルとリモートのコンピューティング ターゲットの両方にデプロイできます。

この拡張機能は、Azure Machine Learning のために異なる数種類のリモート コンピューティング ターゲットをサポートしています。 Azure Machine Learning で[サポートされるコンピューティング ターゲットの完全な一覧](how-to-set-up-training-targets.md)を参照してください。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>VS Code で Azure Machine Learning 用のコンピューティング ターゲットを作成する

**コンピューティング ターゲットを作成するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

2. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 アニメーション画像では、サブスクリプション名は 'Free Trial' であり、ワークスペースは 'TeamWorkspace' です。 

3. ワークスペース ノードの下の **[コンピューティング]** ノードを右クリックし、**[Create Compute]\(コンピューティングの作成)** を選択します。

4. 一覧からコンピューティング ターゲットの種類を選択します。 

5. 高度なプロパティがある場合は、新しいタブで開かれる JSON 構成ファイルで指定します。このファイルでは、コンピューティング ターゲットの一意の名前を指定できます。

6. コンピューティング ターゲットの構成を完了したら、右下にある **[送信]** をクリックします。

Azure Machine Learning コンピューティング (AMLCompute) の例を次に示します。[![VS Code で AML コンピューティングを作成する](./media/vscode-tools-for-ai/CreateAMLCompute.gif)](./media/vscode-tools-for-ai/CreateAMLCompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>VS Code で実験用のリモート コンピューティングを使用する

トレーニング時にリモート コンピューティング ターゲットを使用するには、実行構成ファイルを作成する必要があります。 このファイルは Azure Machine Learning に、実験を実行する場所だけでなく、環境を準備する方法も指示します。

#### <a name="the-run-configuration-file"></a>"実行構成" ファイル

VS Code 拡張機能は、ローカル コンピューター上で、**ローカル**環境用と **docker** 環境用の実行構成を自動的に作成します。

これは、既定の実行構成ファイルからのスニペットです。

すべてのライブラリ/依存関係を自分自身でインストールする場合は、`userManagedDependencies: True` を設定してから、ローカルの実験実行で、VS Code Python 拡張機能によって指定されるとおりの既定の Python 環境を使用します。

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>conda の依存関係ファイル

既定では、新しい conda 環境が自動的に作成され、インストールの依存関係が管理されます。 ただし、`aml_config/conda_dependencies.yml` ファイルで実際の依存関係を指定する必要があります。

これは、既定の "aml_config/conda_dependencies.yml" からのスニペットです。
構成ファイルで他の依存関係を追加できます。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>モデルをトレーニングして調整する

Azure Machine Learning for VS Code (プレビュー) を使用して、コードをすばやく反復処理したり、ステップ実行やデバッグを行ったり、最適なソース コード管理ソリューションを使用したりします。 

**Azure Machine Learning を使用して実験を実行するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 

1. ワークスペース ノードの下の **[コンピューティング]** ノードを展開し、使おうとしているコンピューティングの **[Run Config]\(実行構成)** を右クリックします。 

1. **[Run Experiment]\(実験を実行する)** を選択します。

1. **[View Experiment Run]\(実験実行を表示する)** をクリックし、統合された Azure Machine Learning ポータルを表示して、トレーニングされたモデルの実行の監視と表示を行います。

## <a name="deploy-and-manage-models"></a>モデルを展開して管理する
Azure Machine Learning では、機械学習モデルをクラウド内やエッジ上に展開し、管理することができます。 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>VS Code から Azure Machine Learning にモデルを登録する

モデルのトレーニングを終えたので、それをワークスペースに登録できます。
登録されたモデルは、追跡とデプロイが可能です。

**モデルを登録するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** を右クリックし、**[Register Model]\(モデルの登録)** を選択します。

1. 一覧から、**モデル ファイル** (単一モデルの場合) と**モデル フォルダー** (Tensorflow など、複数のファイルがあるモデルの場合) のどちらをアップロードするかを選択します。 

1. フォルダーまたはファイルを選択します。

1. モデルのプロパティの構成を完了したら、右下にある **[送信]** をクリックします。 



### <a name="deploy-your-service-from-vs-code"></a>VS Code からサービスをデプロイする

VS Code を使用して、Web サービスを以下にデプロイできます。
+ Azure コンテナー インスタンス (ACI): テストの場合
+ Azure Kubernetes Service (AKS): 運用の場合 

その場で作成されるので、テストする ACI コンテナーを事前に作成する必要はありません。 ただし、AKS クラスターは事前に構成する必要があります。 

ここで、[Azure Machine Learning を使用したデプロイ](how-to-deploy-and-where.md)全般について、詳しく説明しています。

**Web サービスをデプロイするには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** ノードを展開します。

1. デプロイするモデルを右クリックし、コンテキスト メニューから **[Deploy Service from Registered Model]\(登録済みモデルからサービスをデプロイする)** コマンドを選択します。

1. コマンド パレットで、デプロイするコンピューティング ターゲットを一覧から選択します。 

1. フィールドに、このサービスの名前を入力します。 

1. コマンド パレットで、キーボードの Enter キーを押して、スクリプト ファイルを参照および選択します。

1. コマンド パレットで、キーボードの Enter キーを押して、conda の依存関係ファイルを参照および選択します。

1. サービスのプロパティの構成を完了したら、右下にある **[送信]** をクリックします。 このサービス プロパティ ファイルでは、使用する可能性のあるローカル Docker ファイルまたは schema.json ファイルを指定できます。

これで Web サービスがデプロイされました。

## <a name="next-steps"></a>次の手順

VS Code の外部で Machine Learning を使用するトレーニングのチュートリアルについては、[Azure Machine Learning を使用したモデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。

コードをローカルで編集、実行、デバッグする手順については、[Python Hello World のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください
