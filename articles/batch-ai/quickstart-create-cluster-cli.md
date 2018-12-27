---
title: Azure クイック スタート - Batch AI クラスターの作成 - Azure CLI | Microsoft Docs
description: クイック スタート - 機械学習および AI モデルのトレーニングに使用する Batch AI クラスターを作成する - Azure CLI
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 1ea12c9a544704ea91b85ae944e611e6769b5592
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407135"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Batch AI トレーニング ジョブ用のクラスターを作成する

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

このクイック スタートでは、Azure CLI を使用して、AI および機械学習モデルのトレーニングに使用できる Batch AI クラスターを作成する方法について説明します。 Batch AI は、データ サイエンティストや AI 研究者が、Azure 仮想マシンのクラスター上で、AI および機械学習モデルを大規模にトレーニングするためのマネージド サービスです。

クラスターには、最初は単一の GPU ノードがあります。 このクイック スタートを完了すると、スケールアップしてモデルのトレーニングに使用できるクラスターが作成されます。 Batch AI、[Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ツール、または [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) を使用して、トレーニング ジョブをクラスターに送信します。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.38 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

このクイック スタートでは、Cloud Shell 内またはローカル コンピューター上の Bash シェルでコマンドを実行することを前提としています。

## <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus2* に作成します。 Batch AI サービスが利用可能な場所 (米国東部 2 など) を選択してください。

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Batch AI クラスターを作成する

まず、`az batchai workspace create` コマンドを使用して、Batch AI "*ワークスペース*" を作成します。 Batch AI クラスターや他のリソースを整理するためのワークスペースが必要です。

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Batch AI クラスターを作成するには、`az batchai cluster create` コマンドを使用します。 次の例では、次のプロパティを持つクラスターを作成します。

* NVIDIA Tesla K80 GPU を 1 つ備えた、NC6 VM サイズの単一ノードが含まれます。 
* コンテナー ベースのアプリケーションをホストするように設計された既定の Ubuntu Server イメージを実行します。これは、ほとんどのトレーニング ワークロードに使用できます。 
* *myusername* という名前のユーザー アカウントを追加し、SSH キーを生成します (ローカル環境の既定のキーの場所 (*~/.ssh*) にまだ存在しない場合)。 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

コマンド出力には、クラスターのプロパティが示されます。 ノードを作成して起動するまで数分かかります。 クラスターの状態を確認するには、`az batchai cluster show` コマンドを実行します。 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

クラスター作成の初期段階では、出力は次のようになり、クラスターが `resizing` 状態であることが示されます。

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
状態が `steady` になり、単一ノードが `Idle` になったら、クラスターを使用できるようになります。

## <a name="list-cluster-nodes"></a>クラスター ノードを表示する 

アプリケーションをインストールしたり、メンテナンスを実行したりするために、クラスター ノード (この例では単一ノード) に接続する必要がある場合は、`az batchai cluster node list` コマンドを実行して接続情報を取得します。


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

JSON 出力は次のようになります。

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
この情報を使用して、ノードへの SSH 接続を作成します。 たとえば、次のコマンドでノードの正しい IP アドレスに置き換えます。

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
SSH セッションを終了して続行します。

## <a name="resize-the-cluster"></a>クラスターのサイズを変更する

クラスターを使用してトレーニング ジョブを実行する場合、より多くのコンピューティング リソースが必要になることがあります。 たとえば、分散トレーニング ジョブ用にサイズを 2 ノードに増やすには、[batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) コマンドを実行します。

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

クラスターのサイズが変更されるまで数分かかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Batch AI のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch AI ワークスペースを使用します。 

ノードの実行中は、Batch AI クラスターに対して課金されます。 実行するジョブがないときにクラスター構成を保持する場合は、クラスターのサイズを 0 ノードに変更します。 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

後でジョブを実行するときは、サイズを 1 ノード以上に変更します。 クラスターが不要になったら、`az batchai cluster delete` コマンドを使用して削除します。

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

Batch AI リソースのリソース グループが不要になったら、`az group delete` コマンドを使用して削除できます。 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI を使用して Batch AI クラスターを作成する方法について説明しました。 Batch AI クラスターを使用してモデルをトレーニングする方法については、ディープ ラーニング モデルのトレーニングに関するクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [ディープ ラーニング モデルをトレーニングする](./quickstart-tensorflow-training-cli.md)
