---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "80628418"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>リージョン別の提供状況
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>ポータル


1. [Azure portal](https://portal.azure.com/) にサインインし、スナップショットを作成するディスクに移動します。
1. ディスクで、 **[スナップショットの作成]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="スクリーンショット。選択する必要がある **[+ スナップショットの作成]** が強調表示されたディスクのブレード。":::

1. 使用するリソース グループを選択し、名前を入力します。
1. **[増分]** を選択し、 **[確認および作成]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="スクリーンショット。選択する必要がある **[+ スナップショットの作成]** が強調表示されたディスクのブレード。":::

1. **[作成]**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="スクリーンショット。選択する必要がある **[+ スナップショットの作成]** が強調表示されたディスクのブレード。":::

## <a name="next-steps"></a>次のステップ

.NET を使用し、増分スナップショットの差分機能を示すサンプル コードを見る場合、[増分スナップショットの差分機能で別のリージョンに Azure マネージド ディスク バックアップをコピーする](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)方法に関するページを参照してください。
