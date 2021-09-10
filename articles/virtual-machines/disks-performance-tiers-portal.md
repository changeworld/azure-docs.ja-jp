---
title: Azure portal を利用して Azure マネージド ディスクのパフォーマンスを変更する
description: Azure portal を利用し、新規と既存のマネージド ディスクのパフォーマンス レベルを変更する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1320ec212a94245e42c63f583d37b33eaa76224e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433298"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Azure portal を利用してパフォーマンス レベルを変更する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>作業の開始

### <a name="new-disks"></a>新しいディスク

次の手順は、ディスクを最初に作成するとき、ディスクのパフォーマンス レベルを変更する方法を示しています。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 新しいディスクを作成する VM に移動します。
1. 新しいディスクを選択するとき、まず、必要なディスクのサイズを選択します。
1. サイズを選択したら、別のパフォーマンス レベルを選択し、そのパフォーマンスを変更します。
1. **[OK]** を選択してディスクを作成します。

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="ディスク作成ブレードのスクリーンショット。1 つのディスクが強調表示されています。パフォーマンス レベルのドロップダウンが強調表示されています。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


### <a name="change-the-performance-tier-of-an-existing-disk-without-downtime"></a>ダウンタイムなしで既存のディスクのパフォーマンス レベルを変更する

ダウンタイムなしでパフォーマンス レベルを変更することもできるため、レベルを変更するために VM の割り当てを解除したり、ディスクをデタッチしたりする必要はありません。

### <a name="prerequisites"></a>前提条件

ダウンタイムなしでディスクのパフォーマンス レベルを変更するには、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、お使いのサブスクリプションに対してこの機能を有効にしてください。

1.  次のコマンドを実行して、お使いのサブスクリプションにこの機能を登録します

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  この機能を試す前に、次のコマンドを使用して、登録状態が **Registered** であることを確認してください (数分かかる場合があります)。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>パフォーマンス レベルを変更する

機能が登録されたら、関連するディスクのパフォーマンス レベルをダウンタイムなしで変更することができます。

1. このリンク ([https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview)) から Azure portal にサインインします。
1. 変更するディスクが含まれる VM に移動します。
1. ディスクを選択します
1. **[Size + Performance]\(サイズおよびパフォーマンス\)** を選択します。
1. **[パフォーマンス レベル]** ドロップダウンで、ディスクの現在のパフォーマンス レベル以外のレベルを選択します。
1. **[サイズ変更]** を選択します。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="[Size + Performance]\(サイズおよびパフォーマンス\) ブレードのスクリーンショット。パフォーマンス レベルが強調表示されています。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>次の手順

より高いパフォーマンス レベルを利用できるようにディスクのサイズを変更する必要がある場合は、次の記事を参照してください。

- [Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](linux/expand-disks.md)
- [Windows 仮想マシンにアタッチされたマネージド ディスクを拡張する](windows/expand-os-disk.md)
