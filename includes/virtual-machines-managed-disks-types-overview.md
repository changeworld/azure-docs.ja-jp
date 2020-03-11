---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262557"
---
現在、Azure マネージド ディスクには 4 種類のディスクがあり、それぞれの種類は特定の顧客シナリオを対象としています。

## <a name="disk-comparison"></a>ディスクの比較

次の表では、使用するものを決定するときの参考に、マネージド ディスクの Ultra Disk、Premium ソリッド ステート ドライブ (SSD)、Standard SSD、Standard HDD (ハード ディスク ドライブ) を比較します。

|   | Ultra Disk   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|ディスクの種類   |SSD   |SSD   |SSD   |HDD   |
|シナリオ   |[SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md) やトップ レベルのデータベース (たとえば SQL や Oracle) などの I/O 集約型のワークロードと、その他のトランザクションが多いワークロード。   |運用環境のワークロードやパフォーマンスに影響されやすいワークロード   |Web サーバー、あまり使用されていないエンタープライズ アプリケーション、および開発/テスト   |バックアップ、重要ではない、不定期に起こるアクセス   |
|最大ディスク サイズ   |65,536 ギビバイト (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大スループット   |2,000 MiB/秒    |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra Disk

Azure Ultra Disk では、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージが提供されます。 Ultra Disk のその他のメリットとして、仮想マシン (VM) を再起動する必要なしに、ワークロードに合わせてディスクのパフォーマンスを動的に変更できます。 Ultra ディスクは、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなどのデータ集中型のワークロードに適しています。 Ultra Disk は、データ ディスクとしてのみ使用できます。 OS ディスクとしては Premium SSD を使用することをお勧めします。

### <a name="performance"></a>パフォーマンス

Ultra ディスクをプロビジョニングするときは、ディスクの容量とパフォーマンスを個別に構成することができます。 Ultra Disk は、4 GiB から最大 64 TiB までの複数の固定されたサイズで提供されます。IOPS とスループットを個別に構成できる柔軟なパフォーマンス構成モデルであることが特徴です。

Ultra Disk の主な機能は次のとおりです。

- ディスク容量:Ultra Disk の容量の範囲は 4 GiB から最大 64 TiB です。
- ディスク IOPS: Ultra Disk は、300 IOPS/GiB の IOPS 制限をサポートし、ディスクあたり最大 160 K の IOPS をサポートします。 プロビジョニングした IOPS を達成するには、選択したディスクの IOPS が VM の IOPS 制限未満であることを確認してください。 ディスクあたりに保証されている最小 IOPS は 2 IOPS/GiB で、全体のベースラインの最小値は 100 IOPS です。 たとえば、4 GiB の Ultra Disk が 1 つある場合、最小値は 8 IOPS ではなく 100 IOPS になります。
- ディスク スループット: Ultra Disk では、1 つのディスクのスループットは、プロビジョニングされた IOPS ごとに 256 KiB/秒に制限され、ディスクあたり最大 2000 MBps に制限されます (MBps = 秒あたり 10^6 バイト)。 各ディスクで保証されている最小スループットは、プロビジョニングされた IOPS ごとに 4 KiB/秒で、全体のベースラインの最小値は 1 MBps です。
- Ultra Disk では、実行時に、ディスクをデタッチすることなく、仮想マシンから、ディスク パフォーマンス属性 (IOPS とスループット) を調整できます。 ディスクでディスク パフォーマンス サイズ変更操作を実行した場合、変更が実際に有効になるまでに最大で 1 時間かかることがあります。 パフォーマンス サイズ変更操作は、24 時間で 4 回に制限されています。 パフォーマンス帯域幅の容量が不足しているために、パフォーマンス サイズ変更操作が失敗する可能性があります。

### <a name="disk-size"></a>ディスク サイズ

|ディスク サイズ (GiB)  |IOPS の上限  |スループットの上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |該当なし         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536 (この範囲内のサイズは 1 TiB ずつ増えます)     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>GA の範囲と制限事項

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
