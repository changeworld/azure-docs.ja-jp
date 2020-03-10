---
title: Dav4 および Dasv4 シリーズ - Azure Virtual Machines
description: Dav4 および Dasv4 シリーズ VM の仕様。
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162181"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 および Dasv4 シリーズ

Dav4 シリーズと Dasv4 シリーズは、マルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを使用する新しいサイズです。最大で 256 MB の L3 キャッシュを備え、8 コアのそれぞれにその L3 キャッシュの 8 GB が専用に割り当てられ、汎用ワークロードを実行するためのカスタマー オプションが増えます。 Dav4 シリーズと Dasv4 シリーズは、D および Dsv3 シリーズと同じメモリおよびディスク構成を備えています。

## <a name="dav4-series"></a>Dav4 シリーズ

ACU: 230-260

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

Dav4 シリーズのサイズは、2.35Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。このプロセッサでは 3.35 GHz のブースト最大周波数を達成できます。 Dav4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、および一時ストレージの組み合わせが提供されます。 データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium SSD を使用するには、Dasv4 サイズを使用します。 Dasv4 サイズの価格および課金の計算方法は、Dav4 シリーズと同じです。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000/46/23   | 2/1,000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2/2,000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4/4,000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8/8,000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8/1,6000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup>これらのサイズはプレビュー段階です。  これらのより大きなサイズをお試しになりたい場合は、[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview) でサインアップしてください。

## <a name="dasv4-series"></a>Dasv4 シリーズ

ACU: 230-260

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

Easv4 シリーズのサイズは、2.35Ghz AMD EPYC<sup>TM</sup> 7452 プロセッサをベースにしています。このプロセッサでは 3.35 GHz のブースト最大周波数を達成し、Premium SSD を使用できます。 Dasv4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、および一時ストレージの組み合わせが提供されます。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2/1,000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2/2,000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4/4,000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8/8,000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8/1,6000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup>これらのサイズはプレビュー段階です。  これらのより大きなサイズをお試しになりたい場合は、[https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview) でサインアップしてください。

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
