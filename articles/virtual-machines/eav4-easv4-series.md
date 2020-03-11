---
title: Eav4 シリーズと Easv4 シリーズ - Azure Virtual Machines
description: Eav4 および Easv4 シリーズ VM の仕様。
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164391"
---
# <a name="eav4-and-easv4-series"></a>Eav4 および Easv4 シリーズ

Eav4 シリーズと Easv4 シリーズでは、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを利用しており、ほとんどのメモリ最適化されたワークロードを実行するためのオプションが増えています。 Eav4 シリーズと Easv4 シリーズは、Ev3 および Esv3 シリーズと同じメモリおよびディスク構成を備えています。

## <a name="eav4-series"></a>Eav4 シリーズ

ACU: 230 ～ 260

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション:サポートされています

メモリ保持更新:サポートされています

Eav4 シリーズのサイズは、2.35 Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。このプロセッサでは 3.35 GHz のブースト最大周波数を達成し、Premium SSD を使用できます。 Eav4 シリーズのサイズは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。 データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium SSD を使用するには、Easv4 シリーズのサイズを使用します。 Easv4 サイズの料金および課金の計算方法は、Eav3 シリーズと同じです。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3000/46/23|2/1,000 |
| Standard\_E4a\_v4|4|32|100|8|6000/93/46|2/2,000 |
| Standard\_E8a\_v4|8|64|200|16|12000/187/93|4/4,000 |
| Standard\_E16a\_v4|16|128|400|32|24000/375/187|8/8,000 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8/10000 |
| Standard\_E32a\_v4|32|256|800|32|48000/750/375|8/1,6000 |
| Standard\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standard\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standard\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  これらのサイズはプレビュー段階です。 これらのより大きなサイズをお試しになりたい場合は、[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview) でサインアップしてください。

## <a name="easv4-series"></a>Easv4 シリーズ

ACU: 230 ～ 260

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新:サポートされています

Easv4 シリーズのサイズは、2.35 Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。このプロセッサでは 3.35 GHz のブースト最大周波数を達成し、Premium SSD を使用できます。 Easv4 シリーズのサイズは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2/1,000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2/2,000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4/4,000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8/8,000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8/10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8/1,6000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  これらのサイズはプレビュー段階です。 これらのより大きなサイズをお試しになりたい場合は、[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview) でサインアップしてください。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
