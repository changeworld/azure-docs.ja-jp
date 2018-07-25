---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 512f251a91a035d3d48566c414076b1a5b6d8805
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37907102"
---
ストレージ最適化済み VM サイズは高いディスク スループットと IO を実現し、ビッグ データ、SQL、NoSQL データベースに最適です。 この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。 

Ls シリーズでは、[Intel® Xeon® プロセッサ E5 v3 ファミリ](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)を使用し、最大 32 個の vCPU を提供します。 Ls シリーズは、G/GS シリーズと同じ CPU パフォーマンスであり、vCPU あたり 8 GiB のメモリを搭載しています。  

## <a name="ls-series"></a>Ls シリーズ

ACU: 180 ～ 240

Premium Storage:  サポートされています

Premium Storage Caching:  サポートされていません
 
| サイズ          | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000/125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000/500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000/1,000     | 8 / 20,000 | 
 

Ls シリーズの VM で実現可能な最大ディスク スループットは、接続されたディスクの数、サイズ、ストライピングによって制限される場合があります。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../articles/virtual-machines/windows/premium-storage.md)」を参照してください。

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

## <a name="size-table-definitions"></a>サイズ表の定義

- ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
- ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
- VM のパフォーマンスを最適にするには、データ ディスクの数を vCPU あたり 2 ディスクに制限する必要があります。
- **想定ネットワーク帯域幅**は、すべての宛先について、すべての NIC で [VM の種類ごとに割り当てられた最大集約帯域幅](../articles/virtual-network/virtual-machine-network-throughput.md)です。 上限は保証されませんが、目的のアプリケーションに適した VM の種類を選択するためのガイダンスを示しています。 実際のネットワークのパフォーマンスは、ネットワークの輻輳、アプリケーションの負荷、ネットワーク設定など、さまざまな要因に左右されます。 ネットワーク スループットの最適化については、[Windows および Linux のネットワーク スループットの最適化](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)に関する記事を参照してください。 Linux または Windows で想定ネットワーク パフォーマンスを実現するには、特定のバージョンを選択するか、VM を最適化することが必要になることがあります。 詳細については、[仮想マシンのスループットを確実にテストする方法](../articles/virtual-network/virtual-network-bandwidth-testing.md)に関する記事を参照してください。
