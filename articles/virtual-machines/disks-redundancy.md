---
title: Azure マネージド ディスクの冗長性オプション
description: Azure マネージド ディスクのゾーン冗長ストレージとローカル冗長ストレージについて説明します。
author: roygara
ms.author: rogarana
ms.date: 09/01/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3748b896956e058e9dc6c01083c19aa8e9c327e7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569354"
---
# <a name="redundancy-options-for-managed-disks"></a>マネージド ディスクの冗長性オプション

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure マネージド ディスクには、ゾーン冗長ストレージ (ZRS) と、ローカル冗長ストレージという 2 つのストレージ冗長性オプションが用意されています。 ZRS では、ローカル冗長ストレージ (LRS) より高いマネージド ディスクの可用性を提供します。 ただし、LRS ディスクでは 1 つのデータ センター内の 3 つのコピーにデータを同期的に書き込むため、LRS ディスクの書き込み待機時間は ZRS ディスクより優れています。

## <a name="locally-redundant-storage-for-managed-disks"></a>マネージド ディスクのローカル冗長ストレージ

ローカル冗長ストレージ (LRS) では、選択されたリージョンにある 1 つのデータ センター内でデータを 3 回レプリケートします。 LRS は、サーバー ラックとドライブの障害からデータを保護します。 自然災害やその他の問題などのゾーン障害から LRS ディスクを保護するには、次の手順を実行します。

- 2 つのゾーンにデータを同期的に書き込み、災害期間中は別のゾーンに自動的にフェールオーバーできるアプリケーションを使用します。
    - たとえば、SQL Server AlwaysOn です。
- ZRS スナップショットを使用して LRS ディスクの頻繁なバックアップを取得します。
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) 経由で LRS ディスクのゾーン間のディザスター リカバリーを有効にします。 ただし、ゾーン間のディザスター リカバリーではゼロ RPO (回復ポイントの目標) は提供されません。

ワークフローでゾーン間のアプリケーション レベルの同期書き込みがサポートされていないか、またはアプリケーションがゼロ RPO を満たす必要がある場合は、ZRS ディスクが理想的です。

## <a name="zone-redundant-storage-for-managed-disks"></a>マネージド ディスクのゾーン冗長ストレージ

ゾーン冗長ストレージ (ZRS) では、選択したリージョンにある 3 つの Azure 可用性ゾーン間で Azure マネージド ディスクを同期的にレプリケートします。 各可用性ゾーンは、独立した電源、冷却装置、ネットワークを備えた独立した物理的な場所です。

ZRS ディスクを使用すると、可用性ゾーンの障害から復旧できます。 ゾーンが停止した場合は、ZRS ディスクを別のゾーン内の仮想マシン (VM) にアタッチできます。 ZRS ディスクを VM 間で共有することで、SQL FCI、SAP ASCS/SCS、GFS2 などのクラスター化または分散化されたアプリケーションの可用性を向上させることもできます。 共有 ZRS ディスクを異なるゾーン内のプライマリおよびセカンダリ VM にアタッチすると、ZRS と[可用性ゾーン](../availability-zones/az-overview.md)の両方を利用できます。 プライマリ ゾーンで障害が発生した場合は、[SCSI 永続的予約](disks-shared-enable.md#supported-scsi-pr-commands)を使用して、セカンダリ VM にすばやくフェールオーバーできます。

### <a name="billing-implications"></a>課金への影響

詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

### <a name="comparison-with-other-disk-types"></a>他のディスクの種類との比較

書き込みの待機時間が長いことを除けば、ZRS を使用するディスクは LRS を使用するディスクと同じであり、スケール ターゲットは同じです。 [ディスクのベンチマークを実行](disks-benchmarks.md)してアプリケーションのワークロードをシミュレートし、LRS と ZRS のディスクの待機時間を比較してください。

### <a name="limitations"></a>制限事項

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>次のステップ

- ZRS ディスクを作成する方法については、[ZRS マネージド ディスクのデプロイ](disks-deploy-zrs.md)に関するページを参照してください。
