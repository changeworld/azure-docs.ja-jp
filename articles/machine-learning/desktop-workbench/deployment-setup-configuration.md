---
title: Azure Machine Learning モデル管理のセットアップと構成 | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning でのモデル管理の設定と構成に関連する手順および概念について説明します。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 150114184f6f04f22aa9da409758daa6a0d175b5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368500"
---
# <a name="model-management-setup"></a>モデル管理のセットアップ

このドキュメントでは、Azure ML モデル管理を使用して Machine Learning モデルを Web サービスとしてデプロイおよび管理する方法について説明します。 

Azure ML モデル管理を使用すると、SparkML、Keras、TensorFlow、Microsoft Cognitive Toolkit、Python などの、いくつかのフレームワークを使用して構築された Machine Learning モデルを効率的にデプロイおよび管理できます。 

このドキュメントの最後までには、モデル管理の環境をセットアップし、Machine Learning モデルをデプロイする準備ができるようになります。

## <a name="what-you-need-to-get-started"></a>はじめにやるべきこと
このガイドを最大限に活用するには、モデルをデプロイできる Azure サブスクリプションまたはリソース グループに対する共同作成者アクセス権が必要です。
Azure Machine Learning Workbench および [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) には CLI がプレインストールされています。

## <a name="using-the-cli"></a>CLI の使用
ワークベンチからコマンド ライン インターフェイス (CLI) を使用するには、**[ファイル]** -> **[Open Command Prompt] \(コマンド プロンプトを開く)** をクリックします。 

データ サイエンス仮想マシン上で、接続してコマンド プロンプトを開きます。 「`az ml -h`」と入力してオプションを表示します。 コマンドの詳細については、--help フラグを使用します。

その他のすべてのシステムでは、CLI をインストールする必要があります。

>[!NOTE]
> Linux DSVM 上の Jupyter Notebook では、以下のコマンド形式を使用して Azure CLI と Azure ML CLI にアクセスできます。  **これは、特に Linux DSVM 上の Jupyter Notebook 向けです**。  これらのコマンドでは、Notebook 内の現在の Python カーネル (たとえば、conda `py35` 環境) にアクセスします
>```
>import sys
>! {sys.executable} -m azure.cli login
>! {sys.executable} -m azure.cli ml -h
>```

### <a name="installing-or-updating-on-windows"></a>Windows へのインストール (または更新)

https://www.python.org/ から Python をインストールします。 pip のインストールを選択したことを確認してください。

[管理者として実行] を使用してコマンド プロンプトを開き、次のコマンドを実行します。

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Linux へのインストール (または更新)
コマンド ラインから次のコマンドを実行し、プロンプトに従います。

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Linux での Docker の構成
root 以外のユーザーが使用する Linux 上の Docker を構成するには、「[Post-installation steps for Linux (Linux でのインストール後の手順)](https://docs.docker.com/engine/installation/linux/linux-postinstall/)」にある手順に従います。

>[!NOTE]
> Linux DSVM では、下のスクリプトを実行して Docker を正しく構成できます。 **このスクリプトを実行した後、ログアウトしてログインし直すことを忘れないでください。**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>モデルをデプロイする
モデルを Web サービスとしてデプロイするには、CLI を使用します。 Web サービスはローカルに、またはクラスターにデプロイできます。

ローカル デプロイから始め、そのモデルとコードの動作を検証してから、運用スケールの使用のためにクラスターにデプロイします。

開始するには、デプロイメント環境をセットアップする必要があります。 環境のセットアップは 1 回限りのタスクです。 セットアップが完了したら、以降のデプロイのためにその環境を再利用できます。 詳細については、次のセクションを参照してください。

環境のセットアップを完了した場合:
- Azure にサインインするよう求められます。 サインインするには、Web ブラウザーを使用して https://aka.ms/devicelogin ページを開き、認証するためのコードを入力します。
- 認証プロセス中に、認証するためのアカウントを入力するよう求められます。 重要: 有効な Azure サブスクリプションと、アカウントにリソースを作成するための十分なアクセス許可を持つアカウントを選択してください。 ログインが完了したら、サブスクリプション情報が表示され、選択されたアカウントで続行するかどうかを確認するメッセージが表示されます。

### <a name="environment-setup"></a>環境のセットアップ
セットアップ プロセスを開始するには、次のコマンドを入力していくつかの環境プロバイダーを登録する必要があります。

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>ローカル デプロイ
ローカル コンピューター上で Web サービスをデプロイおよびテストするには、次のコマンドを使用してローカル環境をセットアップします。 リソース グループ名は省略可能です。

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>ローカル Web サービスのデプロイでは、ローカル コンピューターに Docker をインストールする必要があります。 
>

ローカル環境のセットアップ コマンドは、サブスクリプション内に次のリソースを作成します。
- リソース グループ (指定されていない場合、または指定された名前が存在しない場合)
- ストレージ アカウント
- Azure Container Registry (ACR)
- Application Insights アカウント

セットアップが正常に完了したら、次のコマンドを使用して、使用される環境を設定します。

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>クラスター デプロイ
高スケールの運用シナリオには、クラスター デプロイを使用します。 これは、Kubernetes を含む ACS クラスターをオーケストレーターとして設定します。 ACS クラスターは、Web サービス呼び出しのためのより高いスループットを処理するようにスケールアウトできます。

Web サービスを実稼働環境にデプロイするには、まず次のコマンドを使用して環境をセットアップします。

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

クラスター環境のセットアップ コマンドは、サブスクリプション内に次のリソースを作成します。
- リソース グループ (指定されていない場合、または指定された名前が存在しない場合)
- ストレージ アカウント
- Azure Container Registry (ACR)
- Azure Container Service (ACS) クラスター上の Kubernetes デプロイ
- Application Insights アカウント

>[!IMPORTANT]
> クラスター環境を正常に作成するには、Azure サブスクリプションまたはリソース グループに対する共同作成者アクセス権を持っている必要があります。

リソース グループ、ストレージ アカウント、および ACR はすぐに作成されます。 ACS のデプロイには、最大 20 分かかる場合があります。 

進行中のクラスター プロビジョニングの状態を確認するには、次のコマンドを使用します。

```azurecli
az ml env show -n [environment name] -g [resource group]
```

セットアップが完了したら、このデプロイに使用される環境を設定する必要があります。 次のコマンドを使用します。

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> 環境が作成された後、以降のデプロイでは、上の設定コマンドを使用してそれを再利用するだけで済みます。
>

### <a name="create-a-model-management-account"></a>モデル管理アカウントを作成する
モデルをデプロイするには、モデル管理アカウントが必要です。 これをサブスクリプションごとに 1 回実行する必要があり、複数のデプロイで同じアカウントを再利用できます。

新しいアカウントを作成するには、次のコマンドを使用します。

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

既存のアカウントを使用するには、次のコマンドを使用します。
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

このプロセスの結果として、環境の準備ができ、モデル管理アカウントが作成されて Machine Learning モデル管理とデプロイに必要な機能が提供されます (概要については、「[Azure Machine Learning のモデル管理](model-management-overview.md)」を参照してください)。

## <a name="next-steps"></a>次の手順

* Web サービスをデプロイしてローカル コンピューターまたはクラスターで実行する方法の詳細については、「[Machine Learning モデルを Web サービスとしてデプロイする](model-management-service-deploy.md)」に進んで確認してください。
* ギャラリーにある多数のサンプルのうちの 1 つを試してください。
