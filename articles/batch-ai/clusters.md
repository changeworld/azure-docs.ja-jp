---
title: Azure Batch AI クラスターの使用 | Microsoft Docs
description: Batch AI クラスターの構成を選択し、作成し、AI クラスターを管理する方法
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 9af8ce84805e48dd3c91dd7fb4fcf0b136fbfc60
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410093"
---
# <a name="work-with-batch-ai-clusters"></a>Batch AI クラスターの使用 

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

この記事では、Azure Batch AI でクラスターを使用する方法について説明します。 ここでは、クラスターの概念、可能な構成の種類、そして例を紹介します。 この記事内のクラスターを作成し、管理する例のほとんどは、Azure CLI を使用します。 ただし、Azure portal および Azure Batch AI SDK を含むその他のツールを使用してもクラスターを使用することができます。

Batch AI クラスターは、サービス内のいくつかのリソースの 1 つです。 [Batch AI リソースの概要](resource-concepts.md)を参照し、サービス内のクラスターのスコープを理解します。

## <a name="introduction-to-clusters"></a>クラスターの紹介

Batch AI でのクラスターには、機械学習と AI トレーニング ジョブを実行するためのコンピューティング リソースが含まれています。 クラスター内のすべてのノードには、同じ VM のサイズと OS イメージがあります。 Batch AI には、さまざまなニーズに合わせてカスタマイズされたクラスターを作成するためのさまざまなオプションが用意されています。 通常、プロジェクトを完了するために必要な処理能力の各カテゴリに異なるクラスターを設定します。 要求と予算に応じてクラスター内のノードの数を上下に増減できます。 クラスターをプロビジョニングして、チーム間で共有、または個人で独自のクラスターをプロビジョニングできます。 

各クラスターは、*ワークスペース*という名前の Batch AI リソースの下に存在します。 任意のクラスターをプロビジョニングする前に、Batch AI のワークスペースの設定が必要です。 たとえば、Azure CLI を使用する場合、 [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create)コマンドを使用してワークスペースを設定します。 

クラスターを作成した後は、キューに一度に 1 つのジョブを送信できます。 次にBatch AI は、クラスターでジョブを実行するためのリソース割り当てプロセスを処理します。 

## <a name="cluster-configuration-options"></a>クラスター構成オプション

クラスターを計画するときは、まず、コンピューティング要件を特定します。 Batch AI は、クラスターをニーズに合わせて調整できるように、柔軟な構成オプションを提供します。 以下は、クラスターをプロビジョニングするときに考慮すべき主なオプションです:

* **VM サイズ** - クラスター ノードに[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用可能な任意の[VM サイズ](../virtual-machines/linux/sizes.md)を選択します。 各クラスターに含めることができる VM サイズは１つのみため、タスクが複数の種類の VM を必要とする場合、コンピューティング要件の種類ごとに別のクラスターをプロビジョニングする必要があります。 機械学習や、GPU を活用したフレームワークで開発された AI モデルをトレーニングするには、Azure の[GPU 最適化済みの VM サイズ](../virtual-machines/linux/sizes-gpu.md)を参照してください。
  
* **VM の優先度** - Batch AI クラスターは、専用または低優先度の VM を提供しています。 専用 VM は、クラスターで使用するために確保されています。 優先順位の低いオプションでは、Azure が VM を再要求した場合にジョブを割り込む可能性と引き換えに、大幅にコストを削減して使用されていない Azure VM の容量を割り当てます。 優先順位の低い VM で 24 時間より長く実行されるジョブも、自動的に割り込まれます。 予算が懸念される場合は、短い実験ジョブに優先順位の低い VM の使用を検討してください。 次に、長いジョブの実行する時がきたら専用 VM に切り替えます。

* **ノードの数** - Batch AI には、クラスター内のノードの数の手動および自動スケールのオプションが用意されています。 手動オプションでは、クラスターのスケールをアップ/ダウンするタイミングを制御し、コンピューティング コストを管理できるようにします。 自動スケールのオプションでは、コンピューティング コストを最小限に抑えるため、使用しないときにはクラスターを常に スケールダウンします。 

  クラスターを手動でスケールする選択をする場合は、クラスターの作成時に初期ターゲットを定義します。 ターゲットは、Batch AI によって最初に割り当てられるノードの数です。 後ほど、ノードの数を手動でサイズ変更することができます。  

  自動スケールのオプションを選択する場合は、クラスターの作成時にターゲット ノードの最大および最小数を定義します。 ターゲットは 0 から[Batch AI のコア クォータ](quota-limits.md)でサポートされているノードの最大数の間の範囲です。 ターゲットを0にすると、コンピューティング コストをかけずにクラスター構成を維持できます。 クォータ制限がサポートするよりも高いターゲットを要求すると、プロビジョニングは失敗します。 

* **VM イメージ** - Batch AI は、コンテナー ワークロードをサポートする既定の Ubuntu Server イメージを使用して、規定でクラスターの VM をプロビジョニングします。 [データ サイエンス仮想マシン](../machine-learning/data-science-virtual-machine/overview.md)など、既に構成されている別の Linux イメージを Azure Marketplace から選択することができます。 

* **Storage** - Batch AI には、Azure CLI を使用してクラスターを作成する場合に選択することができる自動ストレージ オプションが提供されています。 このオプションは、新しいストレージ アカウントで Azure ファイル共有と Blob コンテナーを自動的に作成します。 これらのストレージ リソースは、実行時にクラスター内の各ノードにマウントされ、ローカル パスからファイルにアクセスできるようにします。 ストレージ アカウント名、ファイル共有名、Blob コンテナー名、およびマウントパスには全て規定値があり、また追加のパラメーターを使用してクラスターの作成中にカスタマイズが可能です。 

  ストレージ オプションが定義されていない場合は、ストレージ リソースを別に作成して、ジョブを送信するときに定義する必要があります。 このオプションは、クラスターが組織レベルで管理されているが、ストレージはユーザー レベルで管理されている場合に便利です。 Azure Storage にファイルをアップロードし、実行時間中にアクセスする方法の詳細については、 [Azure Storage を使用して Batch AI ジョブの入力と出力を格納する](use-azure-storage.md)を参照してください。

* **ユーザー アクセス** - Batch AI によって、クラスターの作成中に公開および秘密SSH キー ファイルを生成、または個々のノードに SSH ができるよう各自の SSH キーを提供することができます。 ユーザー名 (既定では、現在のユーザーとして設定) とパスワードを定義することもできます。 これらの資格情報は、さまざまなメトリックを表示する、またはさらにジョブの理解を深めるために、実行中のノードへのアクセスに使用できます。

* **セットアップ タスク** - Batch AI では、割り当て時に各ノードで実行されるコマンドライン引数を定義できます。 出力ファイルを記録するパスを定義することもできます。 基本イメージを超える追加のプロビジョニング手順がある場合は、このオプションを使用します。

* **追加の構成** - より専門的な構成が必要になる、いくつかのあまり一般的でないシナリオがあります。 この場合、[クラスター構成ファイル](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file)を Azure CLI コマンドに添付してクラスターを作成します。 

## <a name="create-the-cluster"></a>クラスターを作成する

クラスターの構成オプションを決定したら、クラスターを作成するのに Azure CLI、Azure portal または Batch AI API を使用します。 たとえば、Azure CLI を使用してクラスターを作成するには、必要な構成が提供される正確なコメントを作成するために [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create)ドキュメントに従ってください。 

最も基本的な構成では、次のコマンドがノードが1つの Standard_NC6 および SSH アクセスをプロビジョニングします。 既定では、このクラスターには、最新の既定の Ubuntu Server イメージを実行する専用の VM が含まれています。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

次の例は、自動的に0から4つのノードをスケーリングし、優先度の低いノードと自動ストレージ アカウントを使用するStandard_NC6をプロビジョニングします。 この設定は、低コストで簡単に管理できるクラスターが必要な場合に適切な構成です。 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

次の例では、Data Science Virtual Machine イメージ、カスタム ストレージとマウント オプション、カスタムの SSH 資格情報、*解凍*パッケージをインストールするセットアップ タスク、そして追加セットアップ用のクラスター構成ファイルを含む Standard_NC6 クラスターをプロビジョニングファイルします。 この構成は、個別のニーズに合わせてさらにカスタマイズされたクラスターの一例です。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>クラスターを監視する

[az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list)、 [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show)、そして[az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list)コマンドを使用して、各クラスターのさまざまな情報を監視できます。

### <a name="list-all-clusters"></a>すべてのクラスターの表示

次のコマンドで、ワークスペース内の全てのクラスターを一覧表示します。

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>クラスター に関する情報の表示

次のコマンドは、テーブル形式で、特定のクラスターに関する詳細な情報を示します。

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

自動ストレージのオプションを使用してクラスターをプロビジョニングした場合は、スクリプトやトレーニング ジョブをアップロードするときに使用するストレージ アカウント名を取得します。 次のコマンドを使用します。

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

出力は次のようになります。

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>クラスター ノードの表示

クラスター ノードに接続する必要がある場合、次のコマンドは、ノードと接続情報の一覧を取得します。  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

各ノードの出力は次のようになります:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
この情報を使用すると、次のようなコマンドを使用してノードに SSH 接続を作成することができます。

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>クラスターにジョブを送信する

クラスターをプロビジョニングした後、ノードで実行するジョブを送信できます。 Azure CLI を使用してジョブを準備、送信、および監視する様々な方法については[az batchai job](/cli/azure/batchai/job?view=azure-cli-latest)コマンドを参照してください。 

## <a name="downscale-cluster-for-later-use"></a>クラスターをスケール ダウンして後で使用する

ジョブの実行が終了したら、クラスターをサイズダウンします。 コンピューティング コストを節約するために、使用しない時は常にクラスターをスケールダウンすることを推奨します。 クラスターを0ノードにスケール ダウンすると、今後クラスターを再度スケールアップする必要があるときに再プロビジョニングの必要なく、課金を停止することができます。 自動スケールを選択した場合、クラスターは自動的に最小ターゲットまでスケールダウンされます。 手動スケールを選択した場合、次のコマンドを使用してクラスターをスケールダウンします。

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>クラスターの削除

クラスターの使用が終了したら、 [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete)コマンドを使用して削除します。

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

リソース グループを削除すると、そのリソース グループでプロビジョニングされたすべてのクラスターも自動的に削除されます。

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>次の手順

Batch AI クラスターを作成する例については、[Portal](quickstart-create-cluster-portal.md)または[Azure CLI](quickstart-create-cluster-cli.md)クイックスタート、または GitHub での[Batch AI recipes](https://github.com/Azure/BatchAI/tree/master/recipes)を参照してください。
