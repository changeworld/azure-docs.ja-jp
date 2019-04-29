---
title: Visual Studio Code を使用する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning for Visual Studio Code をインストールし、Azure Machine Learning で簡単な実験を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995253"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code の使用を開始する

この記事では、**Azure Machine Learning for Visual Studio Code** 拡張機能を使用して、Visual Studio Code (VS Code) で Azure Machine Learning サービスを使用して機械学習およびディープ ラーニング モデルをトレーニングおよびデプロイする方法について説明します。

Azure Machine Learning service は、実験をローカルおよびリモート コンピューティング ターゲット上で実行するためのサポートを提供します。 どの実験についても、必要な頻度で複数の実行を追跡し、異なる手法やハイパーパラメーターなどを反復して試することができます。 Azure Machine Learning を使用してカスタム メトリックや実験の実行を追跡し、データ サイエンスの再現性と監査機能を実現できます。

また、これらのモデルをテストや運用のニーズのために展開できます。

## <a name="prerequisites"></a>前提条件

+ Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

+ Visual Studio Code をインストールする必要があります。 VS Code とは、デスクトップ上で実行される、軽量にもかかわらず強力なソース コード エディターです。 Python などの組み込みサポートが付属します。  [VS Code のインストール方法を確認](https://code.visualstudio.com/docs/setup/setup-overview)してください。

+ [Python 3.5 以上をインストール](https://www.anaconda.com/download/)します。


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Azure Machine Learning for VS Code 拡張機能をインストールする

**Azure Machine Learning** 拡張機能をインストールすると、他の 2 つの拡張機能も自動的にインストールされます (インターネットに接続している場合)。 [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能と [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 拡張機能です。

Azure Machine Learning を使用するには、VS Code を Python IDE に変換する必要があります。 [Visual Studio Code で Python](https://code.visualstudio.com/docs/languages/python) を使用するためには、Microsoft Python 拡張機能が必要です。これは、Azure Machine Learning 拡張機能と一緒に自動的にインストールされます。 この拡張機能を使用すると VS Code が優れた IDE になります。この機能は、あらゆるオペレーティング システムでさまざまな Python インタープリターと連携します。 これは、VS Code のすべての機能を活用して、オートコンプリートと IntelliSense、lint 処理、デバッグ、単体テストを提供する他に、複数の Python 環境 (仮想環境や conda 環境など) を切り替えることもできます。 Python コードを編集、実行、デバッグする手順については、[Python Hello World のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください

**Azure Machine Learning 拡張機能をインストールするには:**

1. VS Code を起動します。

1. ブラウザーで、[Azure Machine Learning for Visual Studio Code (プレビュー)](https://aka.ms/vscodetoolsforai) 拡張機能にアクセスします。

1. この Web ページで **[インストール]** をクリックします。 

1. 拡張機能のタブで **[インストール]** をクリックします。

1. VS Code において拡張機能の [ようこそ] タブが開き、Azure のシンボル (下の画像内で赤い枠に囲まれている) がアクティビティ バーに追加されます。

   ![Visual Studio Code のアクティビティ バーの Azure アイコン](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. ダイアログ ボックスで、**[サインイン]** をクリックし、画面に表示されるプロンプトに従い Azure の認証を行います。 
   
   Azure Machine Learning for VS Code 拡張機能と一緒にインストールされた Azure アカウント拡張機能が、Azure アカウントの認証に役立ちます。 [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)のページでコマンドの一覧表を参照してください。

> [!Tip] 
> [VS Code の IntelliCode 拡張機能 (プレビュー)](https://go.microsoft.com/fwlink/?linkid=2006060) を確認してください。 IntelliCode は、現在のコード コンテキストに基づいた最も関連性の高いオートコンプリートの推測など、Python における IntelliSense に一連の AI 支援機能を提供します。

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK のインストール

1. Python 3.5 以上をインストールし、VS Code によって認識されていることを確認してください。 ここでインストールする場合は、VS Code を再起動し、 https://code.visualstudio.com/docs/python/python-tutorial の手順に従って Python インタープリターを選択します。

1. 統合ターミナル ウィンドウで、使用する Python インタープリターを指定します。または、**Enter** キーを押すと既定の Python インタープリターを使用できます。

   ![インタープリターを選択する](./media/vscode-tools-for-ai/python.png)

1. ウィンドウの右下隅に、Azure ML SDK が自動的にインストールされていることを示す通知が表示されます。    Azure Machine Learning service を使用するための Visual Studio Code の前提条件を備えた、ローカルのプライベート Python 環境が作成されます。

   ![Azure Machine Learning SDK for Python をインストールする](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning の利用を開始

VS Code を使用して機械学習モデルのトレーニングとデプロイを開始する前に、モデルとリソースを格納するための [Azure Machine Learning service ワークスペース](concept-azure-machine-learning-architecture.md#workspace)をクラウドに作成する必要があります。 1 つ作成して、そのワークスペースに最初の実験を作成する方法について説明します。

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

   [![インストール](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure サブスクリプションを右クリックし、**[ワークスペースの作成]** を選択します。 一覧が表示されます。 アニメーション画像では、サブスクリプション名は "Free Trial" であり、ワークスペースは "TeamWorkspace" です。 

1. 一覧から既存のリソース グループを選択するか、コマンド パレットでウィザードを使用して新しいリソース グループを作成します。

1. フィールドに、新しいワークスペースの一意でわかりやすい名前を入力します。 スクリーンショットでは、ワークスペースの名前は "TeamWorkspace" です。

1. Enter を押すと、新しいワークスペースが作成されます。 これは、ツリー内でサブスクリプション名の下に表示されます。

1. 実験ノードを右クリックし、コンテキスト メニューから **[Create Experiment]\(実験の作成\)** を選択します。  実験は、Azure Machine Learning を使用した実行を記録します。

1. フィールドに、実験の名前を入力します。 スクリーン ショットでは、実験の名前は "MNIST" です。
 
1. Enter を押すと、新しい実験が作成されます。 これは、ツリー内でワークスペース名の下に表示されます。

1. ワークスペースで [実験] を右クリックして [アクティブな実験として設定] を選択できます。 **'アクティブな'** 実験とは現在使用中の実験のことであり、VS Code で開いたフォルダーは、クラウド上でこの実験にリンクされます。 このフォルダーには ローカルの Python スクリプトが含まれます。

   ここで、それぞれの実験を実験と一緒に実行すると、すべての主要メトリックが実験履歴に格納されます。トレーニングするモデルは、自動的に Azure Machine Learning にアップロードされ、実験のメトリックとログと一緒に格納されます。

   [![VS Code でフォルダーを接続](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>コンピューティング ターゲットを作成して管理する

Azure Machine Learning for VS Code を使用すると、データを準備し、モデルをトレーニングして、それらをローカルとリモートのコンピューティング ターゲットの両方にデプロイできます。

この拡張機能は、Azure Machine Learning のために異なる数種類のリモート コンピューティング ターゲットをサポートしています。 Azure Machine Learning で[サポートされるコンピューティング ターゲットの完全な一覧](how-to-set-up-training-targets.md)を参照してください。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>VS Code で Azure Machine Learning 用のコンピューティング ターゲットを作成する

**コンピューティング ターゲットを作成するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

2. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 アニメーション画像では、サブスクリプション名は 'Free Trial' であり、ワークスペースは 'TeamWorkspace' です。 

3. ワークスペース ノードの下の **[コンピューティング]** ノードを右クリックし、**[Create Compute]\(コンピューティングの作成)** を選択します。

4. 一覧からコンピューティング ターゲットの種類を選択します。 

5. コマンド パレットで、仮想マシンのサイズを選択します。

6. コマンド パレットで、フィールドにコンピューティング ターゲットの名前を入力します。 

7. 高度なプロパティがある場合は、新しいタブで開かれる JSON 構成ファイルで指定します。最大ノード数などのプロパティを指定できます。

8. コンピューティング ターゲットの構成を完了したら、画面の右下にある **[送信]** をクリックします。

Azure Machine Learning コンピューティング (AMLCompute) の作成例を次に示します。[![VS Code で AML コンピューティングを作成する](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>"実行構成" ファイル

VS Code 拡張機能によって、ローカル コンピューター上で、ローカル コンピューティング ターゲットと、**ローカル**環境用と **docker** 環境用の実行構成が自動的に作成されます。 関連付けられているコンピューティング ターゲット ノード下に、実行構成ファイルが見つかります。 

## <a name="train-and-tune-models"></a>モデルをトレーニングして調整する

Azure Machine Learning for VS Code (プレビュー) を使用して、コードをすばやく反復処理したり、ステップ実行やデバッグを行ったり、最適なソース コード管理ソリューションを使用したりします。 

**Azure Machine Learning を使用して実験をローカルに実行するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 

1. ワークスペース ノードの下の **[コンピューティング]** ノードを展開し、使用するコンピューティングの **[実行構成]** を右クリックします。 

1. **[Run Experiment]\(実験を実行する)** を選択します。

1. ファイル エクスプローラーから、実行するスクリプトを選択します。 

1. **[View Experiment Run]\(実験実行を表示する)** をクリックし、統合された Azure Machine Learning ポータルを表示して、トレーニングされたモデルの実行の監視と表示を行います。

ローカルでの実験の実行例を次に示します。[![ローカルでの実験の実行](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>VS Code で実験用のリモート コンピューティングを使用する

トレーニング時にリモート コンピューティング ターゲットを使用するには、実行構成ファイルを作成する必要があります。 このファイルは Azure Machine Learning に、実験を実行する場所だけでなく、環境を準備する方法も指示します。

#### <a name="the-conda-dependencies-file"></a>conda の依存関係ファイル

既定では、新しい conda 環境が自動的に作成され、インストールの依存関係が管理されます。 ただし、`aml_config/conda_dependencies.yml` ファイルで実際の依存関係とそのバージョンを指定する必要があります。 

これは、既定の "aml_config/conda_dependencies.yml" からのスニペットです。 たとえば、次に示すように "tensorflow=1.12.0" と指定できます。 依存関係のバージョンを指定しない場合、最新のバージョンが使用されます。  
構成ファイルで他の依存関係を追加できます。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Azure Machine Learning を使用してリモート コンピューティング ターゲット上で実験を実行するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。 

1. エディター ウィンドウで Python スクリプトを右クリックし、**[AML: Run as Experiment in Azure]\(AML: Azure で実験として実行\)** を選択します。 

1. コマンド パレットで、コンピューティング ターゲットを選択します。 

1. コマンド パレットで、フィールドに実行構成名を入力します。 

1. 実験の実行時の依存関係を指定して conda_dependencies.yml ファイルを編集した後、画面右下の **[送信]** をクリックします。 

1. **[View Experiment Run]\(実験実行を表示する)** をクリックし、統合された Azure Machine Learning ポータルを表示して、トレーニングされたモデルの実行の監視と表示を行います。

リモート コンピューティング ターゲット上での実験の実行例を次に示します。[![リモート ターゲット上での実験の実行](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>モデルを展開して管理する
Azure Machine Learning では、機械学習モデルをクラウド内やエッジ上に展開し、管理することができます。 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>VS Code から Azure Machine Learning にモデルを登録する

モデルのトレーニングを終えたので、それをワークスペースに登録できます。
登録されたモデルは、追跡とデプロイが可能です。

**モデルを登録するには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** を右クリックし、**[Register Model]\(モデルの登録)** を選択します。

1. コマンド パレットで、フィールドにモデル名を入力します。 

1. 一覧から、**モデル ファイル** (単一モデルの場合) と**モデル フォルダー** (Tensorflow など、複数のファイルがあるモデルの場合) のどちらをアップロードするかを選択します。 

1. フォルダーまたはファイルを選択します。

1. モデルのプロパティの構成を完了したら、画面の右下にある **[送信]** をクリックします。 

モデルの AML への登録例を次に示します。[![AML へのモデルの登録](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>VS Code からサービスをデプロイする

VS Code を使用して、Web サービスを以下にデプロイできます。
+ Azure コンテナー インスタンス (ACI): テストの場合
+ Azure Kubernetes Service (AKS): 運用の場合 

その場で作成されるので、テストする ACI コンテナーを事前に作成する必要はありません。 ただし、AKS クラスターは事前に構成する必要があります。 

ここで、[Azure Machine Learning を使用したデプロイ](how-to-deploy-and-where.md)全般について、詳しく説明しています。

**Web サービスをデプロイするには:**

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 Azure Machine Learning のサイドバーが表示されます。

1. ツリー ビューで、お使いの Azure サブスクリプションと Azure Machine Learning サービス ワークスペースを展開します。

1. ワークスペース ノードの下の **[モデル]** ノードを展開します。

1. デプロイするモデルを右クリックし、コンテキスト メニューから **[Deploy Service from Registered Model]\(登録済みモデルからサービスをデプロイする)** コマンドを選択します。

1. コマンド パレットで、デプロイするコンピューティング ターゲットを一覧から選択します。 

1. コマンド パレットで、このサービスの名前をフィールドに入力します。  

1. コマンド パレットで、キーボードの Enter キーを押して、スクリプト ファイルを参照および選択します。

1. コマンド パレットで、キーボードの Enter キーを押して、conda の依存関係ファイルを参照および選択します。

1. サービスのプロパティの構成を完了したら、画面の右下にある **[送信]** をクリックしてデプロイします。 このサービス プロパティ ファイルでは、使用する可能性のあるローカルの Docker ファイルまたは schema.json ファイルを指定できます。

これで Web サービスがデプロイされました。

Web サービスのデプロイ例を次に示します。[![Web サービスのデプロイ](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>キーボード ショートカットの使用

VS Code のほとんどの機能と同様に、VS Code の Azure Machine Learning 機能もキーボードからアクセスできます。 知っておくべき最も重要なキーの組み合わせは、コマンド パレットを表示する Ctrl + Shift + P です。 ここで、よく使う操作のキーボード ショートカットを含め、VS Code のすべての機能にアクセスできます。

[![Azure Machine Learning for VS Code のキーボード ショートカット](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>次の手順

VS Code の外部で Machine Learning を使用するトレーニングのチュートリアルについては、[Azure Machine Learning を使用したモデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。

コードをローカルで編集、実行、デバッグする手順については、[Python Hello World のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください
