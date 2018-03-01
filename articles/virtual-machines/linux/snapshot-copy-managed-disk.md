---
title: "Azure で VHD のスナップショットを作成する | Microsoft Docs"
description: "バックアップまたは問題のトラブルシューティングに使うために、Azure で VHD のコピーを作成する方法について説明します。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>スナップショットの作成 

バックアップ、または VM の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを作成します。 スナップショットは、VHD の完全な読み取り専用コピーです。 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Azure CLI 2.0 使ってスナップショットを作成する

次の例では、Azure CLI 2.0 がインストールされていて、Azure アカウントにログインしている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

次の手順は、`az snapshot create` コマンドと `--source-disk` パラメーターを使用してスナップショットを取得する方法を示しています。 次の例では、`myResourceGroup` リソース グループの管理 OS ディスクを使って `myVM` という名前の VM が作成されているものとします。

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

出力は次のようになります。

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal を使ってスナップショットを作成する 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上の **[リソースの作成]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。

スナップショットを使って Managed Disk を作成し、高パフォーマンスが必要な VM に接続する計画がある場合は、`az snapshot create` コマンドで `--sku Premium_LRS` パラメーターを使います。 Premium Managed Disk として保存されるようにスナップショットが作成されます。 Premium Managed Disks はソリッド ステート ドライブ (SSD) なので高パフォーマンスですが、料金は Standard ディスク (HDD) より高くなります。


## <a name="next-steps"></a>次の手順

 スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)」のスクリプトを参照してください。

