---
title: Azure ディスク プールをプロビジョニング解除する (プレビュー)
description: Azure ディスク プールをプロビジョニング解除、停止、および削除する方法について説明します。
author: roygara
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 823dd066e1751896b3f7986a18d18d80d68219a1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469872"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Azure ディスク プールをプロビジョニング解除する (プレビュー)

この記事では、Azure ディスク プールの削除プロセス (プレビュー) と、iSCSI サポートを無効にする方法について説明します。

## <a name="stop-a-disk-pool"></a>ディスク プールを停止する

ディスク プールを停止してコストを節約し、すべての構成を保持できます。 ディスク プールを停止すると、iSCSI 経由でディスク プールに接続できなくなります。 ディスク プールをサポートするためにデプロイされたマネージド リソースは削除されません。 ディスク プールを停止する前に、ディスク プールへの iSCSI 接続があるすべてのクライアントを最初に切断する必要があります。 ディスク プールは、いつでも開始できます。 その場合、このディスク プールで公開されている iSCSI ターゲットが再アクティブ化されます。
# <a name="portal"></a>[ポータル](#tab/azure-portal)

現在、Azure portal ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name 'myDiskPool' -ResourceGroupName 'myResourceGroupt'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool stop --name "myDiskPool" --resource-group "myResourceGroup"
```
---

## <a name="delete-a-disk-pool"></a>ディスク プールを削除する

ディスク プールを削除すると、マネージド リソース グループ内のすべてのリソースも削除されます。 ディスク プールへの未処理の iSCSI 接続がある場合は、ディスク プールを削除できません。 ディスク プールへの iSCSI 接続があるすべてのクライアントを最初に切断する必要があります。 ディスク プールに追加されているディスクは削除されません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. "**ディスク プール**" を検索して選択し、削除するディスク プールを選択します。
1. ペインの上部にある **[削除]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPool -Name "myDiskpoolName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool delete --name "myDiskPool" --resource-group "myResourceGroup"
```

---

## <a name="disable-iscsi-support"></a>iSCSI サポートを無効にする

ディスク プールで iSCSI サポートを無効にすると、ディスク プールに接続できなくなります。

ディスク プールで iSCSI サポートを初めて有効にしたとき、iSCSI ターゲットが iSCSI 接続のエンドポイントとして作成されます。 この iSCSI ターゲットを削除することで、ディスク プールでの iSCSI サポートを無効にできます。 各ディスク プールに構成できる iSCSI ターゲットは 1 つのみです。

既存のディスク プールで iSCSI サポートを再び有効にできます。 ディスク プールへの未処理の iSCSI 接続がある場合は、ディスク プールで iSCSI サポートを無効にすることはできません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. "**ディスク プール**" を検索し、対象のディスク プールを選択します。
1. **[設定]** で **[iSCSI]** を選択します。
1. **[Enable iSCSI]\(iSCSI を有効にする\)** チェック ボックスをオフにし、 **[保存]** を選択します。    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "myDiskpoolName" -Name "myiSCSITargetName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "myDiskPool" --name "myIscsiTarget" --resource-group "myResourceGroup"
```

---

## <a name="next-steps"></a>次のステップ

[Azure マネージド ディスク](managed-disks-overview.md)について学習します。