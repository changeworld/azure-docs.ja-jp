---
title: Azure Machine Learning での Visual Studio Code Tools for AI 拡張機能の使用
description: Visual Studio Code Tools for AI について説明し、VS Code で Azure Machine Learning サービスを使用して機械学習とディープ ラーニングのモデルについてのトレーニングを開始し、それらを展開する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 6372aada8c3d380f8d3cefb0479e45bbd7c63898
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945248"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI: VS コードから ML モデルのトレーニングと展開を行う
この記事では、**VS Code Tools for AI** 拡張機能を使用して、VS Code で Azure Machine Learning サービスを利用する機械学習とディープ ラーニングのモデルをトレーニングし、それらを展開する方法を説明します。

Azure Machine Learning は、実験をローカルおよびリモート コンピューティング ターゲットで実行するためのサポートを提供します。 どの実験についても、必要な頻度で複数の実行を追跡し、異なる手法やハイパーパラメーターなどを反復して試することができます。 Azure Machine Learning を使用してカスタム メトリックや実験の実行を追跡し、データ サイエンスの再現性と監査機能を実現できます。

また、これらのモデルをテストや運用のニーズのために展開できます。

## <a name="prerequisites"></a>前提条件

+ [VS Code Tools for AI](how-to-vscode-tools.md) が Azure Machine Learning 用にセットアップされている。

+ VS Code を使用して [Python 用の Azure Machine Learning SDK がインストールされている](how-to-vscode-tools.md)。

+ Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-and-manage-compute-targets"></a>コンピューティング ターゲットを作成して管理する

Visual Studio Code Tools for AI では、データを準備し、モデルをトレーニングして、それらをローカルとリモートの両方のコンピューティング ターゲットに展開することができます。

この拡張機能は、Azure Machine Learning のために異なる数種類のリモート コンピューティング ターゲットをサポートしています。 Azure Machine Learning で[サポートされるコンピューティング ターゲットの完全な一覧](how-to-set-up-training-targets.md)を参照してください。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>VS Code で Azure Machine Learning 用のコンピューティング ターゲットを作成する

**コンピューティング ターゲットを作成するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

2. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 アニメーション画像では、サブスクリプション名は "OpenMind Studio"、ワークスペースは "MyWorkspace" になっています。 

3. ワークスペース ノードの下の **[コンピューティング]** ノードを右クリックし、**[Create Compute]\(コンピューティングの作成)** を選択します。

4. 一覧からコンピューティング ターゲットの種類を選択します。 

5. フィールドに、このコンピューティング ターゲットの一意の名前を入力し、仮想マシンのサイズを指定します。

6. 高度なプロパティがある場合は、新しいタブで開かれる JSON 構成ファイルで指定します。 

7. コンピューティング ターゲットの構成を終えたら、右下の **[完了]** をクリックします。

Azure Batch AI 用の例を次に示します。[![VS Code で Azure Batch AI コンピューティングを作成する](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

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

VS Code から Azure Machine Learning を使用して、迅速なコードの反復、ステップ実行とデバッグ、希望するソース コード管理ソリューションの使用を行います。 

**Azure Machine Learning を使用して実験を実行するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 アニメーション画像では、サブスクリプション名は "OpenMind Studio"、ワークスペースは "MyWorkspace" になっています。 

1. ワークスペース ノードの下の **[コンピューティング]** ノードを展開し、使おうとしているコンピューティングの **[Run Config]\(実行構成)** を右クリックします。 

1. **[Run Experiment]\(実験を実行する)** を選択します。

1. **[View Experiment Run]\(実験実行を表示する)** をクリックし、統合された Azure Machine Learning ポータルを表示して、トレーニングされたモデルの実行の監視と表示を行います。

   [![VS Code から機械学習実験を実行する](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

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

1. ファイル選択ダイアログ ボックスを使用して、ファイルまたはフォルダーを選択します。

   [![コンピューティング](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> ここで、生成された json ファイルからタグを削除してください。

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

1. VS Code のコマンド パレットで、デプロイするコンピューティング ターゲットを一覧から選択します。 

1. フィールドに、このサービスの名前を入力します。 

1. ダイアログ ボックスの右下隅の **[参照]** をクリックし、スコアリング スクリプトを選択します。 ダイアログを閉じます。

1. ローカル Docker ファイルがある場合は、表示される 2 番目のダイアログ ボックスで **[参照]** をクリックします。 

   選択せずにダイアログ ボックスを閉じて、ローカル Docker ファイルを指定しないと、既定では "Azure Machine Learning" という Docker ファイルが使用されます。

1. 表示される 3 番目のダイアログ ボックスで **[参照]** をクリックし、ローカルの conda ファイル パスを選択するか、後から json エディターでファイル パスを入力します。

1. 使用する schema.json ファイルがある場合は、表示される 4 番目のダイアログ ボックスで **[参照]** をクリックし、ファイルを選択します。

これで Web サービスがデプロイされました。

次に、Azure コンテナー インスタンスの例を示します。[![VS Code での Azure コンテナー インスタンス](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>次の手順

VS Code の外部で Machine Learning を使用してトレーニングする手順については、[Azure Machine Learning を使用してモデルをトレーニングすることに関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。

コードをローカルで編集、実行、デバッグする手順については、[Python Hello World のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください
