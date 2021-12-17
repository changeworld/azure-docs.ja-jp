---
title: 増分スナップショットの作成
description: Azure portal、Azure PowerShell モジュール、Azure Resource Manager を使用した作成方法を含む、マネージド ディスクの増分スナップショットについて説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: ba03ec11522ea5a4e4a011d1e62fa09b25aec749
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022306"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>マネージド ディスクの増分スナップショットの作成

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、増分スナップショットを作成することができます。 最新バージョンの Azure CLI が必要です。 Azure CLI を[インストール](/cli/azure/install-azure-cli)または[アップデート](/cli/azure/update-azure-cli)する方法については、以下の記事を参照してください。

次のスクリプトは、特定のディスクの増分スナップショットを作成します。

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

スナップショットの `SourceResourceId` プロパティにより、同じディスクからの増分スナップショットを識別できます。 `SourceResourceId` は親ディスクの Azure Resource Manager リソース ID です。

`SourceResourceId` を利用し、特定のディスクに関連付けられているすべてのスナップショットの一覧を作成できます。 `yourResourceGroupNameHere` を実際の値に変更します。次の例を利用すると、既存の増分スナップショットを一覧表示できます。


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell モジュールを使用して、増分スナップショットを作成することができます。 最新バージョンの Azure PowerShell モジュールが必要になります。 次のコマンドを実行すると、最新版がインストールされるか、既存のインストールが最新版に更新されます。

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

インストールが完了したら、`Connect-AzAccount` を使用して PowerShell セッションにログインします。

Azure PowerShell を利用して増分スナップショットを作成するには、構成を [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) と `-Incremental` パラメーターで設定し、`-Snapshot` パラメーターを介してそれを [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) に変数として渡します。

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

スナップショットの `SourceResourceId` プロパティと `SourceUniqueId` プロパティで同じディスクからの増分スナップショットを識別できます。 `SourceResourceId` は親ディスクの Azure Resource Manager リソース ID です。 `SourceUniqueId` はディスクの `UniqueId` プロパティから継承した値です。 ディスクを削除してから同じ名前でディスクを新規作成する場合、`UniqueId` プロパティの値が変更されます。

`SourceResourceId` と `SourceUniqueId` を利用し、特定のディスクに関連付けられているすべてのスナップショットの一覧を作成できます。 `yourResourceGroupNameHere` を実際の値に変更します。次の例を利用すると、既存の増分スナップショットを一覧表示できます。

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

Azure Resource Manager テンプレートを使用して、増分スナップショットを作成することもできます。 apiVersion が **2019-03-01** に設定されていること、および増分プロパティも true に設定されていることを確認する必要があります。 次のスニペットは、Resource Manager テンプレートを使用して増分スナップショットを作成する方法の例です。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="cross-region-snapshot-copy-preview"></a>リージョン間スナップショット のコピー (プレビュー)

CopyStart オプション (プレビュー) を使用して、任意のリージョンから任意のリージョンへの増分スナップショットのコピーを開始できます。 Azure では増分スナップショットをコピーするプロセスが処理され、最後のスナップショット以降の差分変更だけがターゲット リージョンにコピーされ、データフットプリントが削減されます。 お客様はコピーの進行状況を確認して、ターゲット スナップショットがターゲット リージョン内のディスクを復元する準備ができていることを確認できます。 このプロセスを使用して、長期的な保持のためにスナップショットを別のサブスクリプションにコピーできます。 また、これを使用して同じリージョン内のスナップショットをコピーし、ゾーン冗長ストレージでスナップショットが完全に強化され、[ゾーン障害](disks-redundancy.md#zone-redundant-storage-for-managed-disks)が発生した場合にスナップショットを確実に使用できます。

:::image type="content" source="media/disks-incremental-snapshots/cross-region-snapshot.png" alt-text="[複製] オプションを使用して増分スナップショットのリージョン間コピーを調整した Azure の図。" lightbox="media/disks-incremental-snapshots/cross-region-snapshot.png":::

### <a name="pre-requisites"></a>前提条件

プレビュー機能を使用するには、サブスクリプションで機能を有効にする必要があります。 機能を登録するには、次のコマンドを使用します：

```azurecli
az feature register --namespace Microsoft.Compute --name CreateOptionClone
```

登録が完了するには数分かかる場合があります。次のコマンドを使用して、その状態を確認できます：

```azurecli
az feature show --namespace Microsoft.Compute --name CreateOptionClone
```

### <a name="restrictions"></a>制限

- リージョン間スナップショット のコピーは、現在、米国東部 2 と米国中西部でのみ使用できます。
- バージョン 2020-12-01 以降の Azure Compute Rest API を使用する必要があります。

### <a name="get-started"></a>はじめに

```azurecli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
name=<targetSnapshotName>
sourceSnapshotResourceId=<sourceSnapshotResourceId>
targetRegion=<validRegion>

az login
az account set --subscription $subscriptionId
az group deployment create -g $resourceGroupName \
--template-uri https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/CrossRegionCopyOfSnapshots/CopyStartIncrementalSnapshots.json \
--parameters "name=$name" "sourceSnapshotResourceId=$sourceSnapshotResourceId" "targetRegion=$targetRegion"
az resource show -n $name -g $resourceGroupName --namespace Microsoft.Compute --resource-type snapshots --api-version 2020-12-01 --query [properties.completionPercent] -o tsv
```

## <a name="next-steps"></a>次のステップ

.NET を使用し、増分スナップショットの差分機能を示すサンプル コードを見る場合、[増分スナップショットの差分機能で別のリージョンに Azure マネージド ディスク バックアップをコピーする](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)方法に関するページを参照してください。
