---
title: チュートリアル:機械学習の使用を開始する - Python
titleSuffix: Azure Machine Learning
description: 個人用の開発環境で実行されている Azure Machine Learning SDK for Python の使用を開始する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 69340245cdebda570b526afd4f11ac8314384b43
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210620"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>チュートリアル:個人の開発環境で Azure Machine Learning の使用を開始する (パート 1/4)

この "*4 部構成のチュートリアル シリーズ*" では、Azure Machine Learning の基礎を学び、以下のような Azure クラウド上でジョブベースの Python 機械学習タスクを実行します。 

このチュートリアル シリーズのパート 1 では、次のことを行います。

> [!div class="checklist"]
> * Azure Machine Learning SDK をインストールします。
> * コードのディレクトリ構造を設定します。
> * Azure Machine Learning ワークスペースを作成します。
> * ローカルの開発環境を構成します。
> * モデルをトレーニングするためのクラウドベースのリソースであるコンピューティング クラスターをセットアップします。

このチュートリアル シリーズでは、**バッチ ジョブ** を送信するうえで必要な Azure Machine Learning の概念を重点的に取り上げます。バッチ ジョブでは、コードがクラウドに送信されてユーザーの対話式操作を介さずにバックグラウンドで実行されます。 これは、繰り返し実行したい完成したスクリプトやコードのほか、多くのコンピューティング処理を要する機械学習タスクに役立ちます。

Azure Machine Learning の始め方としては、Jupyter ノートブックの使用もお勧めです。  「[チュートリアル: Jupyter Notebook で Azure Machine Learning の作業を開始する](tutorial-1st-experiment-sdk-setup.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [Azure Machine Learning](https://aka.ms/AMLFree) をお試しいただけます。
- Python の仮想環境を管理してパッケージをインストールするための [Anaconda](https://www.anaconda.com/download/) または [Miniconda](https://www.anaconda.com/download/)。  
- Conda を使い慣れていない場合は、「[Conda の使用を開始する](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)」をご覧ください。
- Python スクリプトを作成するための任意の IDE またはテキスト エディター。

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK をインストールする

このチュートリアル全体を通して、Azure Machine Learning SDK for Python を使用します。 Python の依存関係の問題を避けるために、分離された環境を作成します。 このチュートリアル シリーズでは、Conda を使用してその環境を作成します。 他のソリューション (`venv`、`virtualenv`、docker など) を希望する場合は、必ず Python バージョン 3.5 以上、3.9 未満を使用してください。

ご使用のシステムに Conda がインストールされているかどうかを確認します。
    
```bash
conda --version
```
    
このコマンドから `conda not found` エラーが返される場合は、[Miniconda をダウンロードしてインストール](https://docs.conda.io/en/latest/miniconda.html)してください。 

Conda をインストールしたら、ターミナルまたは Anaconda プロンプト ウィンドウを使用して新しい環境を作成します。

```bash
conda create -n tutorial python=3.8
```

次に、作成した Conda 環境に Azure Machine Learning SDK をインストールします。

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Azure Machine Learning SDK のインストールが完了するまでに約 2 分かかります。
>
> タイムアウト エラーが発生した場合は、代わりに `pip install --default-timeout=100 azureml-core` を実行してみてください。


> [!div class="nextstepaction"]
> [SDK をインストールしました](?success=install-sdk#dir) [問題が発生しました](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>コードのディレクトリ構造を作成する

このチュートリアルでは、次の簡単なディレクトリ構造を設定することをお勧めします。

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="ディレクトリ構造: 最上位の tutorial とサブディレクトリ .azureml":::


- `tutorial`: プロジェクトの最上位ディレクトリ。
- `.azureml`: Azure Machine Learning の構成ファイルを格納するための非表示のサブディレクトリ。

たとえば、Bash ウィンドウでこれを作成するには、次のようにします。

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> グラフィカル ウィンドウで構造を作成したり表示したりするには、まず、隠しファイルと隠しフォルダーを表示および作成できるようにする必要があります。
>
> * Mac の Finder ウィンドウで **Command + Shift + .** キーを使用して、 隠しファイルと隠しフォルダーの表示を切り替えます。  
> * Windows 10 エクスプローラーの場合、[非表示のファイルとフォルダーを表示する方法](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5)に関するページを参照してください。 
> * Linux グラフィカル インターフェイスでは、**Ctrl + H** キーを使用するか、 **[表示]** メニューで **非表示のファイルを表示する** チェック ボックスをオンにします。




> [!div class="nextstepaction"]
> [ディレクトリを作成しました](?success=create-dir#workspace) [問題が発生しました](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Azure Machine Learning ワークスペースの作成

ワークスペースは Azure Machine Learning の最上位レベルのリソースであり、次のことを行うための一元的な場所です。

- コンピューティングなどのリソースを管理する。
- ノートブック、環境、データセット、パイプライン、モデル、エンドポイントなどのアセットを格納する。
- 他のチーム メンバーと共同作業を行う。

次のコードを使用して、`01-create-workspace.py` という新しい Python ファイルを最上位のディレクトリ (`tutorial`) に追加します。 パラメーター (名前、サブスクリプション ID、リソース グループ、[場所](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) を設定に合わせて調整します。

コードは、対話型セッションで、または Python ファイルとして実行できます。

>[!NOTE]
> ローカルの開発環境 (コンピューターなど) を使用している場合は、次のコードを初めて実行するときに、"*デバイス コード*" を使用してワークスペースに対して認証するように求められます。 画面に表示される手順に従って操作します。

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

アクティブになった *tutorial1* Conda 環境が表示されているウィンドウで、`tutorial` ディレクトリから次のコードを実行します。

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> このコードを実行したときにサブスクリプションへのアクセス権がないというエラーが表示される場合は、「[ワークスペースの作成](how-to-manage-workspace.md?tab=python#create-multi-tenant)」で認証オプションについてご確認ください。


*01-create-workspace.py* が正常に実行されると、フォルダー構造は次のようになります。

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="01-create-workspace.py の実行後、ファイル config.json が .azureml サブディレクトリに表示される":::

ファイル `.azureml/config.json` には、Azure Machine Learning ワークスペースに接続するために必要なメタデータが含まれています。 つまり、サブスクリプション ID、リソース グループ、ワークスペース名が含まれています。 

> [!NOTE]
> `config.json` の内容は秘密ではありません。 これらの詳細を共有しても問題はありません。
>
> Azure Machine Learning ワークスペースと対話するには、認証が必要です。

> [!div class="nextstepaction"]
> [ワークスペースを作成しました](?success=create-workspace#cluster) [問題が発生しました](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Azure Machine Learning コンピューティング クラスターを作成する

最上位ディレクトリ (`tutorial`) に、`02-create-compute.py` という Python スクリプトを作成します。 次のコードを入力して、0 から 4 ノードの間で自動スケーリングされる Azure Machine Learning コンピューティング クラスターを作成します。

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

アクティブになった *tutorial1* Conda 環境が表示されているウィンドウで、次の Python ファイルを実行します。

```bash
python ./02-create-compute.py
```


> [!NOTE]
> クラスターが作成されても、プロビジョニングされるノードは 0 個です。 クラスターでは、ジョブを送信するまでコストは発生 "*しません*"。 このクラスターは、2,400 秒 (40 分) の間、アイドル状態になったときにスケールダウンされます。

フォルダー構造は次のようになります。

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="02-create-compute.py を tutorial ディレクトリに追加する":::

> [!div class="nextstepaction"]
> [コンピューティング クラスターを作成しました](?success=create-compute-cluster#next-steps)[問題が発生しました](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>スタジオで表示する

[Azure Machine Learning スタジオ](https://ml.azure.com)にサインインして、作成したワークスペースとコンピューティング インスタンスを表示します。

1. ワークスペースを作成する際に使用した **サブスクリプション** を選択します。
1. 作成した **Machine Learning ワークスペース** (*tutorial-ws*) を選択します。
1. ワークスペースが読み込まれたら、左側の **[Compute]\(コンピューティング\)** を選択します。
1. 上部にある **[Compute clusters]\(コンピューティング クラスター\)** タブを選択します。

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="スクリーンショット: ワークスペースでコンピューティング インスタンスを表示する。":::

このビューには、プロビジョニングされているコンピューティング クラスターが、アイドル状態のノードの数、ビジー状態のノードの数、プロビジョニング解除状態のノードの数と共に表示されます。  このクラスターはまだ使用していないので、現時点ではすべてのノードがプロビジョニング解除状態です。

## <a name="next-steps"></a>次のステップ

この設定チュートリアルでは、次のことを行いました。

- Azure Machine Learning ワークスペースを作成しました。
- ローカルの開発環境を設定しました。
- Azure Machine Learning コンピューティング クラスターを作成する。

このチュートリアルの他のパートでは、次のことについて説明します。

* 第 2 部 Azure Machine Learning SDK for Python を使用してクラウドでコードを実行します。
* 第 3 部 モデルのトレーニングに使用する Python 環境を管理します。
* 第 4 部 データを Azure にアップロードし、トレーニングでそのデータを使用します。

引き続き次のチュートリアルでは、Azure Machine Learning コンピューティング クラスターにスクリプトを送信する手順について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: "Hello world!" Python スクリプトを Azure で実行する](tutorial-1st-experiment-hello-world.md)
