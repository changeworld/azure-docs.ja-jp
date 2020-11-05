---
title: Azure マネージド ディスクのパフォーマンスを変更する
description: マネージド ディスクのパフォーマンス レベルについて、および既存のマネージド ディスクのパフォーマンス レベルを変更する方法について説明します。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4e31af3a66927e0c93caf477a7daf1b86eebf8f5
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348697"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>マネージド ディスクのパフォーマンス レベル (プレビュー)

Azure Disk Storage には現在、短期間の予期しないトラフィックを処理できるように、より高いパフォーマンスを提供する組み込みのバースト機能が備わっています。 Premium SSD には、実際のディスク サイズを増やすことなくディスクのパフォーマンスを向上させる柔軟性があります。 この機能により、ワークロードのパフォーマンスのニーズを満たし、コストを削減することができます。 

> [!NOTE]
> 現在のところ、この機能はプレビュー段階です。 

この機能は、休日のショッピング、パフォーマンス テスト、トレーニング環境の実行など、一貫して高いレベルのパフォーマンスを一時的に必要とするイベントに最適です。 これらのイベントを処理するために、必要に応じて、より高いパフォーマンス レベルを使用できます。 その後、より高いパフォーマンスが必要でなくなったときに、元のレベルに戻すことができます。

## <a name="how-it-works"></a>しくみ

初めてディスクを配置またはプロビジョニングすると、プロビジョニングされたディスク サイズに基づいてそのディスクのベースライン パフォーマンス レベルが設定されます。 より高いパフォーマンス レベルを使用して、より高い要求を満たすことができます。 そのパフォーマンス レベルが必要でなくなった場合は、最初のベースライン パフォーマンス レベルに戻すことができます。

課金はレベルの変更に応じて変動します。 たとえば、P10 ディスク (128 GiB) をプロビジョニングした場合、ベースライン パフォーマンス レベルは P10 (500 IOPS、100 MBps) と設定されます。 P10 の料金で課金されます。 ディスク サイズを増やすことなく、P50 のパフォーマンス (7,500 IOPS、250 MBps) に適合するようにレベルをアップグレードできます。 アップグレードされている期間は、P50 の料金で課金されます。 より高いパフォーマンスが必要でなくなった場合は、P10 レベルに戻すことができます。 ディスクは、再び P10 の料金で課金されます。

| ディスク サイズ | ベースライン パフォーマンス レベル | アップグレードの範囲 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | なし |
| 8 TiB | P60 |  P70、P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | なし |

課金情報については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。

## <a name="restrictions"></a>制限

- 現在、この機能は Premium SSD でのみサポートされています。
- ディスクのレベルを変更する前に、VM の割り当てを解除するか、実行中の VM からディスクを切断する必要があります。
- P60、P70、P80 の各パフォーマンス レベルの使用は、4,096 GiB 以上のディスクに制限されています。
- ディスクのパフォーマンス レベルは、24 時間ごとに 1 回だけダウングレードできます。

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>ベースライン レベルよりも高いレベルで空のデータ ディスクを作成する

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace イメージから、ベースライン レベルよりも高いレベルで OS ディスクを作成する

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>ディスクのレベルを更新する

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>ディスクのレベルを表示する

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>次の手順

より高いパフォーマンス レベルを利用できるようにディスクのサイズを変更する必要がある場合は、次の記事を参照してください。

- [Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](linux/expand-disks.md)
- [Windows 仮想マシンにアタッチされたマネージド ディスクを拡張する](windows/expand-os-disk.md)
