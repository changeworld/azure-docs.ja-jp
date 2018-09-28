---
title: よく使うタスクのための CLI をインストールして使用する - Azure Machine Learning
description: Azure Machine Learning での最も一般的な機械学習タスクのための CLI をインストールして使う方法を説明します。
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06e85845d41b240638a5b5b4d75d64fd460a99bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953329"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Azure Machine Learning でよく使うタスクのための Machine Learning CLI をインストールして使用する

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Azure Machine Learning サービスは、統合されたエンドツーエンドのデータ サイエンスと高度な分析を提供するソリューションです。 プロフェッショナルなデータ サイエンティストは、Azure Machine Learning サービスを使用してデータを準備し、実験を開発し、クラウド規模でモデルをデプロイできます。 

Azure Machine Learning で提供されるコマンド ライン インターフェイス (CLI) を使うと、次のことができます。
+ ワークスペースとプロジェクトを管理します
+ コンピューティング ターゲットを設定します
+ トレーニング実験を実行します
+ 過去の実行の履歴とメトリックを表示します
+ Web サービスとして運用環境にモデルをデプロイします
+ 運用環境のモデルとサービスを管理します

この記事では、最もユーザーの役に立つ CLI コマンドをいくつか紹介します。 

![Azure Machine Learning CLI](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>はじめにやるべきこと

モデルをデプロイできる Azure サブスクリプションまたはリソース グループに対する共同作成者アクセス権が必要です。 また、CLI を実行するには、Azure Machine Learning Workbench をインストールする必要があります。 

>[!IMPORTANT]
>Azure Machine Learning サービスで提供される CLI は、Azure リソースの管理に使用される [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) とは異なります。

## <a name="get-and-start-cli"></a>CLI を入手して開始する

この CLI を入手するには、次の場所からダウンロードできる Azure Machine Learning Workbench をインストールします。
    + Windows - https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

CLI を開始するには:
+ Azure Machine Learning Workbench で、**[ファイル] -> [コマンド プロンプトを開く]** メニューから CLI を起動します。

## <a name="get-command-help"></a>コマンドのヘルプを表示する 

CLI コマンドに関する追加情報は、`az ml <xyz> --debug` などのコマンド (`<xyz>` はコマンド名) の後に `--debug` または `--help` を使って表示できます。 例: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Azure Machine Learning の一般的な CLI タスク 

このセクションでは、次のような、CLI を使って実行できる最も一般的なタスクについて説明します。
+ [コンピューティング ターゲットの設定](#target)
+ [リモート実行ジョブの送信](#jobs)
+ [Jupyter Notebook の操作](#jupyter)
+ [実行履歴との対話](#history)
+ [運用化のための環境の構成](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>コンピューティング ターゲットを設定する

次のようなさまざまなターゲットで機械学習モデルを計算できます。
+ ローカル モード内
+ データ サイエンス VM (DSVM) 内
+ HDInsight クラスター上

データ サイエンス VM ターゲットをアタッチするには:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

HDInsight ターゲットをアタッチするには:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

**aml_config** フォルダー内では、Conda の依存関係を変更できます。 

また、GPU DSVM の PySpark、Python、Python を操作することもできます。 

Python の操作モードを定義するには:
+ Python の場合、`Framework:Python` を `<target name>.runconfig` に追加します 

+ PySpark の場合、`Framework:PySpark` を `<target name>.runconfig` に追加します 

+ GPU DSVM 内の Python の場合
    1. `Framework:Python` を `<target name>.runconfig` に追加します 

    1. また、`baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` を `<target name>.compute` に追加します

コンピューティング ターゲットを準備するには:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>サブスクリプションを表示するには:<br/>
>`az account show`<br/>
>
>サブスクリプションを設定するには:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>リモート ジョブを送信する

ジョブをリモート ターゲットに送信するには:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Jupyter Notebook を操作する

Jupyter Notebook を開始するには:
```azurecli
az ml notebook start
```

このコマンドは、Jupyter Notebook を localhost で開始します。 カーネル Python 3 を選ぶとローカルで作業でき、カーネル `<target name>` を選ぶとリモート VM で作業できます。

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>実行履歴と対話して調べる

実行履歴を一覧表示するには:
```azurecli
az ml history list -o table
```

完了したすべての実行を一覧表示するには:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

最高の精度で実行を検索するには:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

各実行によって生成されたファイルをダウンロードすることもできます。 

フォルダー出力に保存されているモデルをレベル上げするには:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

そのモデルをダウンロードするには:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>運用化のための環境を構成する

運用化環境を設定するには、次のものを作成する必要があります。

> [!div class="checklist"]
> * リソース グループ 
> * ストレージ アカウント
> * Azure Container Registry (ACR)
> * Application Insight アカウント
> * Azure Container Service (ACS) クラスター上の Kubernetes デプロイ


Docker コンテナーでのテスト用にローカル デプロイを設定するには:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Kubernetes で ACS クラスターを設定するには:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

デプロイの状態を監視するには:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

使う必要がある環境を設定するには:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>次の手順

概要については次の記事をご覧ください。 
+ [Azure Machine Learning をインストールして開始する](quickstart-installation.md)
+ [Iris データ分類チュートリアル: パート 1](tutorial-classifying-iris-part-1.md)

詳細については次の記事をご覧ください。
+ [モデルを管理するための CLI コマンド](model-management-cli-reference.md)
