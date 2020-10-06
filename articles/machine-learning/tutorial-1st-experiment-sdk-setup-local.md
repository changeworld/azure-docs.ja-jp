---
title: チュートリアル:機械学習の使用を開始する - Python
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、個人用の開発環境で実行されている Azure Machine Learning SDK for Python の使用を開始する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368483"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>チュートリアル:個人の開発環境で Azure Machine Learning の使用を開始する (パート 1/4)

この "*4 部構成のチュートリアル シリーズ*" では、Azure Machine Learning の基礎を学び、以下のような Azure クラウド上でジョブベースの Python 機械学習タスクを実行します。 これらのタスクは次のとおりです。

1. 機械学習に使用するワークスペースとローカル開発環境を設定します。
2. Azure Machine Learning SDK for Python を使用してクラウドでコードを実行します。
3. モデルのトレーニングに使用する Python 環境を管理します。
4. データを Azure にアップロードし、トレーニングでそのデータを使用します。

このチュートリアル シリーズのパート 1 では、次のことを行います。

> [!div class="checklist"]
> * Azure Machine Learning SDK をインストールします。
> * コードのディレクトリ構造を設定します。
> * Azure Machine Learning ワークスペースを作成します。
> * ローカルの開発環境を構成します。
> * コンピューティング クラスターを設定します。

>[!NOTE]
> このチュートリアル シリーズでは、コンピューティング集約型で、再現性を必要とする Python "*ジョブベース*" の機械学習タスクに適した Azure Machine Learning の概念に焦点を当てています。 機械学習のタスクがこのプロファイルに適さない場合は、[Azure Machine Learning コンピューティング インスタンスにおける Jupyter または RStudio の機能](tutorial-1st-experiment-sdk-setup.md)を使用して Azure Machine Learning に移動することができます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [Azure Machine Learning](https://aka.ms/AMLFree) をお試しいただけます。
- Python や[機械学習の概念](concept-azure-machine-learning-architecture.md)に精通していること。 たとえば、環境、トレーニング、スコアリングについてです。
- ローカルの開発環境: Python がインストールされているラップトップとお好きな IDE (Visual Studio Code、PyCharm、Jupyter など)。

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK をインストールする

このチュートリアル全体を通して、Azure Machine Learning SDK for Python を使用します。

このチュートリアル全体を通して使用する環境を設定するには、Conda や pip など、最も使い慣れたツールを使用できます。 pip を使用して Azure Machine Learning SDK for Python を環境にインストールします。

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>コードのディレクトリ構造を作成する
このチュートリアルでは、次の簡単なディレクトリ構造を設定することをお勧めします。

```markdown
tutorial
└──.azureml
```

- `tutorial`: プロジェクトの最上位ディレクトリ。
- `.azureml`: Azure Machine Learning の構成ファイルを格納するための非表示のサブディレクトリ。

## <a name="create-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースの作成

ワークスペースは Azure Machine Learning の最上位レベルのリソースであり、次のことを行うための一元的な場所です。

- コンピューティングなどのリソースを管理する。
- ノートブック、環境、データセット、パイプライン、モデル、エンドポイントなどのアセットを格納する。
- 他のチーム メンバーと共同作業を行う。

次のコードを使用して、`01-create-workspace.py` という新しい Python ファイルを最上位のディレクトリ (`tutorial`) に追加します。 パラメーター (名前、サブスクリプション ID、リソース グループ、[場所](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) を設定に合わせて調整します。

コードは、対話型セッションで、または Python ファイルとして実行できます。

>[!NOTE]
> ローカルの開発環境 (ラップトップなど) を使用する場合は、次のコードを初めて実行するときに、"*デバイス コード*" を使用してワークスペースに対して認証するように求められます。 画面に表示される手順に従って操作します。

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

`tutorial` ディレクトリから次のコードを実行します。

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

上記のコード スニペットを実行した後のフォルダー構造は次のようになります。

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

ファイル `.azureml/config.json` には、Azure Machine Learning ワークスペースに接続するために必要なメタデータが含まれています。 つまり、サブスクリプション ID、リソース グループ、ワークスペース名が含まれています。 

> [!NOTE]
> `config.json` の内容は秘密ではありません。 これらの詳細を共有しても問題はありません。
>
> Azure Machine Learning ワークスペースと対話するには、認証が必要です。

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning コンピューティング クラスターの作成

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
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

次の Python ファイルを実行します。

```bash
python ./02-create-compute.py
```


> [!NOTE]
> クラスターが作成されても、プロビジョニングされるノードは 0 個です。 クラスターでは、ジョブを送信するまでコストは発生 "*しません*"。 このクラスターは、2,400 秒 (40 分) の間、アイドル状態になったときにスケールダウンされます。

フォルダー構造は次のようになります。

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>次のステップ

この設定チュートリアルでは、次のことを行いました。

- Azure Machine Learning ワークスペースを作成しました。
- ローカルの開発環境を設定しました。
- Azure Machine Learning コンピューティング クラスターを作成する。

次のチュートリアルでは、Azure Machine Learning コンピューティング クラスターにスクリプトを送信する手順について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: "Hello world!" Python スクリプトを Azure で実行する](tutorial-1st-experiment-hello-world.md)
