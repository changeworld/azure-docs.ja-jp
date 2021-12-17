---
title: Eav4 シリーズおよび Easv4 シリーズ
description: Eav4 および Easv4 シリーズ VM の仕様。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: ayshak
ms.reviewer: jushiman
ms.openlocfilehash: 5cefbdb7cdb3d44c08c61889d3a6420d31729989
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471746"
---
# <a name="eav4-and-easv4-series"></a>Eav4 および Easv4 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Eav4 シリーズと Easv4 シリーズでは、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを利用しており、メモリ最適化されたほとんどのワークロードを実行するためのオプションが増強されています。 Eav4 シリーズと Easv4 シリーズは、Ev3 および Esv3 シリーズと同じメモリおよびディスク構成を備えています。

## <a name="eav4-series"></a>Eav4 シリーズ

[ACU](acu.md): 230 ～ 260<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br>

Eav4 シリーズのサイズは、3.35 GHz のブースト最大周波数を達成することができる、2.35 Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。 Eav4 シリーズのサイズは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。 データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium SSD を使用するには、Easv4 シリーズのサイズを使用します。 Easv4 サイズの料金および課金の計算方法は、Eav3 シリーズと同じです。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4<sup>1</sup>|2|16|50|4|3000/46/23|2 | 800 |
| Standard\_E4a\_v4|4|32|100|8|6000/93/46|2 | 1600 |
| Standard\_E8a\_v4|8|64|200|16|12000/187/93|4 | 3200 |
| Standard\_E16a\_v4|16|128|400|32|24000/375/187|8 | 6400 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 | 8000 |
| Standard\_E32a\_v4|32|256|800|32|48000/750/375|8 | 12800 |
| Standard\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 | 19200 |
| Standard\_E64a\_v4|64|512|1600|32|96000 / 1000 (500)|8 | 25600 |
| Standard\_E96a\_v4|96|672|2400|32|96000 / 1000 (500)|8 | 32000 |

<sup>1</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 


## <a name="easv4-series"></a>Easv4 シリーズ

[ACU](acu.md): 230 ～ 260<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br>

Easv4 シリーズのサイズは、2.35 Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。このプロセッサでは 3.35 GHz のブースト最大周波数を達成し、Premium SSD を使用できます。 Easv4 シリーズのサイズは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | バースト キャッシュが有効な一時ストレージの最大スループット: IOPS/MBps<sup>1</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4<sup>3</sup>|2|16|32|4|4000 / 32 (50)| 4000/100 |3200 / 48| 4000/200 |2 | 800 |
| Standard_E4as_v4 <sup>2</sup>|4|32|64|8|8000 / 64 (100)| 8000/200 |6400 / 96| 8000/200 |2 | 1600 |
| Standard_E8as_v4 <sup>2</sup>|8|64|128|16|16000 / 128 (200)| 16000/400 |12800 / 192| 16000/400 |4 | 3200 |
| Standard_E16as_v4 <sup>2</sup>|16|128|256|32|32000 / 255 (400)| 32000/800 |25600 / 384| 32000/800 |8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)| 40000/1000 |32000 / 480| 40000/1000 |8 | 8000 |
| Standard_E32as_v4<sup>2</sup>|32|256|512|32|64000 / 510 (800)| 64000/1600 |51200 / 768| 64000/1600 |8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)| 96000/2000 |76800 / 1148| 80000/2000 |8 | 19200 |
| Standard_E64as_v4<sup>2</sup>|64|512|1024|32|128000 / 1020 (1600)| 128000/2000 |80000 / 1200| 80000/2000 |8 | 25600 |
| Standard_E96as_v4 <sup>2</sup>|96|672|1344|32|192000 / 1020 (2400)| 192000/2000 |80000 / 1200| 80000/2000 |8 | 32000 |

<sup>1</sup>  Easv4 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。 <br>
<sup>2</sup> [コア数を制限したサイズも提供しています](./constrained-vcpu.md)。 <br>
<sup>3</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報:[ディスクの種類](./disks-types.md#ultra-disks)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
