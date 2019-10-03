---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224580"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>マネージド ディスクの増分スナップショット (プレビュー) の作成

増分スナップショット (プレビュー) は、マネージド ディスクの特定の時点のバックアップであり、取得時に、最後のスナップショット以降のすべての変更のみで構成されます。 増分スナップショットをダウンロードまたは使用しようとすると、完全な VHD が使用されます。 マネージド ディスク スナップショットに対するこの新しい機能により、必要に応じて個別のスナップショットごとにディスク全体を保存する必要がなくなるため、コスト効率が向上する可能性があります。 通常のスナップショットと同じように、増分スナップショットを使用して、完全なマネージド ディスクを作成したり、通常のスナップショットを作成したりできます。

増分スナップショットと通常のスナップショットには、いくつかの違いがあります。 増分スナップショットでは、ディスクのストレージの種類に関係なく、常に Standard HDD ストレージが使用されますが、通常のスナップショットでは Premium SSD を使用できます。 VM のデプロイをスケールアップするために Premium Storage で通常のスナップショットを使用している場合は、[Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md) の Standard Storage でカスタム イメージを使用することをお勧めします。 これは、より低コストでさらに大規模なスケールを実現するのに役立ちます。 さらに、増分スナップショットは、[ゾーン冗長ストレージ](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) を使用すると、信頼性が向上する可能性があります。 選択したリージョンで ZRS が使用可能な場合、増分スナップショットは ZRS を自動的に使用します。 そのリージョンで ZRS が使用できない場合、スナップショットは既定で[ローカル冗長ストレージ](../articles/storage/common/storage-redundancy-lrs.md) (LRS) に設定されます。 この動作をオーバーライドして手動で選択することもできますが、これはお勧めしません。

増分スナップショットには、マネージド ディスクで独自に使用できる差分機能も用意されています。 これにより、同じマネージド ディスクの 2 つの増分スナップショット間の変更内容を、ブロック レベルまで取得できます。 スナップショットを複数のリージョンにわたってコピーするとき、この機能を使用してデータ フットプリントを削減できます。

まだプレビューにサインアップしておらず、増分スナップショットの使用を開始する場合は、パブリック プレビューにアクセスするために AzureDisks@microsoft.com までメールでお問い合わせください。

## <a name="restrictions"></a>制限

- 増分スナップショットは、現時点ではディスクのサイズを変更した後に作成することはできません。
- 増分スナップショットは、現時点ではサブスクリプション間で移動できません。
- 現時点では、任意の時点で特定のスナップショット ファミリの最大 5 つのスナップショットの SAS URI のみを生成することができます。
- 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。
- ディスクあたり最大 7 つの増分スナップショットを 5 分ごとに作成できます。
- 1 つのディスクに対して合計 200 の増分スナップショットを作成できます。

## <a name="powershell"></a>PowerShell

Azure PowerShell を使用すると、増分スナップショットを作成できます。 最新バージョンの PowerShell をローカルにインストールできます。 Azure PowerShell の最新バージョンが必要になります。次のコマンドを実行すると、最新バージョンがインストールされるか、既存のインストールを最新のバージョンに更新します。

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

インストールが完了したら、`az login` を使用して PowerShell セッションにログインします。

`<yourDiskNameHere>`、`<yourResourceGroupNameHere>`、および `<yourDesiredSnapShotNameHere>` を実際の値に置き換えて、次のスクリプトを使用して増分スナップショットを作成できます。

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager テンプレート

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

## <a name="cli"></a>CLI

Azure CLI で [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) を使用して増分スナップショットを作成できます。 コマンドの例を次に示します。

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

また、[az snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show) で `--query` パラメーターを使用することによって、CLI でどのスナップショットが増分スナップショットであるかを特定することができます。 そのパラメーターを使用して、スナップショットの **SourceResourceId** および **SourceUniqueId** プロパティに直接クエリを実行することができます。 SourceResourceId は、親ディスクの Azure Resource Manager リソース ID です。 **SourceUniqueId** は、ディスクの **UniqueId** プロパティから継承した値です。 ディスクを削除してから同じ名前でディスクを作成した場合、**UniqueId** プロパティの値は変更されます。

それらのクエリの例は、次のようになります。

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>次の手順

まだプレビューにサインアップしておらず、増分スナップショットの使用を開始する場合は、パブリック プレビューにアクセスするために AzureDisks@microsoft.com までメールでお問い合わせください。
