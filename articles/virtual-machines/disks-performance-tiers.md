---
title: Azure マネージド ディスクのパフォーマンスを引き上げる
description: マネージド ディスクのパフォーマンス レベルと、お使いのマネージド ディスクのパフォーマンス レベルをアップグレードする方法について説明します。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974133"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>マネージド ディスクのパフォーマンス レベル (プレビュー)

Azure Disk Storage には現在、短期間の予期しないトラフィックを処理できるように、より高いパフォーマンスを達成する組み込みのバースト機能が備わっています。 Premium SSD には実際のディスク サイズを増やさずにディスク パフォーマンスを向上させる柔軟性があるため、ワークロードのパフォーマンスのニーズに合わせて、コストを削減することができます。この機能は現在プレビュー段階です。 これは、休日のショッピング、パフォーマンス テスト、トレーニング環境の実行など、一貫して高いレベルのパフォーマンスを一時的に必要とするイベントに最適です。 こうしたイベントを処理するために、必要な限り高いパフォーマンス レベルを選択し、追加のパフォーマンスが必要なくなったら元のレベルに戻すことができます。

## <a name="how-it-works"></a>しくみ

初めてディスクを配置またはプロビジョニングすると、プロビジョニングされたディスク サイズに基づいてそのディスクのベースライン パフォーマンス レベルが設定されます。 より高い要求を満たすために高いパフォーマンス レベルを選択することができ、そのパフォーマンスが必要なくなったら、初期のベースライン パフォーマンス レベルに戻すことができます。 たとえば、P10 ディスク (128 GiB) をプロビジョニングした場合、ベースライン パフォーマンス レベルは P10 (500 IOPS、100 MB/秒) と設定されます。 ディスク サイズを増やさずに P50 (7500 IOPS、250 MB/秒) のパフォーマンスに合わせてレベルを更新し、その高いパフォーマンスが必要なくなったら P10 に戻すことができます。

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

## <a name="restrictions"></a>制限

- 現在、premium SSD でのみサポートされています。
- レベルを変更する前に、稼働中の VM からディスクをデタッチする必要があります。
- P60、P70、P80 の各パフォーマンス レベルの使用は、4096 GiB 以上のディスクに制限されています。

## <a name="regional-availability"></a>リージョン別の提供状況

現在、マネージド ディスクのパフォーマンス レベルの調整は、次のリージョンの premium SSD でのみ使用できます。

- 米国中西部 
- 米国東部 2 
- 西ヨーロッパ
- オーストラリア東部 
- オーストラリア南東部 
- インド南部

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>ベースライン レベルよりも高いレベルでデータ ディスクを作成/更新する

1. サンプル テンプレート [CreateUpdateDataDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json) を使用して、ベースライン レベルよりも高いレベルの空のデータ ディスクを作成するか、ディスクのレベルをベースライン レベルよりも高くなるように更新します

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. ディスクのレベルを確認します

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>ベースライン レベルよりも高いレベルで OS ディスクを作成/更新する

1. サンプル テンプレート [CreateUpdateOSDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json) を使用して、マーケットプレース イメージから OS ディスクを作成するか、OS ディスクのレベルをベースライン レベルよりも高くなるように更新します

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. ディスクのレベルを確認します
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>次のステップ

より高いパフォーマンス レベルを利用するためにディスクのサイズを変更する必要がある場合は、次の記事をご覧ください。

- [Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](linux/expand-disks.md)
- [Windows 仮想マシンにアタッチされたマネージド ディスクを拡張する](windows/expand-os-disk.md)