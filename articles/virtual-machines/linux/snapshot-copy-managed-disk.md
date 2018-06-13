---
title: Azure で VHD のスナップショットを作成する | Microsoft Docs
description: バックアップまたは問題のトラブルシューティングに使うために、Azure で VHD のコピーを作成する方法について説明します。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318903"
---
# <a name="create-a-snapshot"></a>スナップショットの作成 

バックアップ、または VM の問題を解決するために、OS ディスクまたはデータ ディスクのスナップショットを作成します。 スナップショットは、VHD の完全な読み取り専用コピーです。 

## <a name="use-azure-cli"></a>Azure CLI の使用 

次の例では、Azure CLI 2.0 がインストールされていて、Azure アカウントにログインしている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

次の手順は、`az snapshot create` コマンドと `--source-disk` パラメーターを使用してスナップショットを取得する方法を示しています。 次の例では、`myResourceGroup` リソース グループに `myVM` という名前の VM があるものとします。

ディスク ID を取得します。
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

*osDisk-backup* という名前のスナップショットを取得します。

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`--sku Standard_ZRS` パラメーターを含める必要があります。

## <a name="use-azure-portal"></a>Azure Portal の使用 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上の **[リソースの作成]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。


## <a name="next-steps"></a>次の手順

 スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)」のスクリプトを参照してください。

