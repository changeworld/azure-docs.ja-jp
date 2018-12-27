---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 36902edd7b2df472960d19b8ef9a4ebd4cdfe695
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906682"
---
この記事では、仮想マシンのサイズの前の世代の情報を提供します。 これらのサイズも、使用できますが、より新しい世代が使用可能です。


## <a name="ds-series"></a>DS シリーズ

ACU: 160

Premium Storage:  サポートされています

Premium Storage Caching:  サポートされています

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000/32 (43) |3,200/32 |2/500 |
| Standard_DS2 |2 |7 |14 |8 |8,000/64 (86) |6,400/64 |2/1,000 |
| Standard_DS3 |4 |14 |28 |16 |16,000/128 (172) |12,800/128 |4/2,000 |
| Standard_DS4 |8 |28 |56 |32 |32,000/256 (344) |25,600/256 |8/4,000 |

<br>

## <a name="ds-series---memory-optimized"></a>DS シリーズ - メモリ最適化済み

ACU: 160 <sup>1</sup>

Premium Storage:  サポートされています

Premium Storage Caching:  サポートされています

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000/64 (72) |6,400/64 |2/1,000 |
| Standard_DS12 |4 |28 |56 |16 |16,000/128 (144) |12,800/128 |4/2,000 |
| Standard_DS13 |8 |56 |112 |32 |32,000/256 (288) |25,600/256 |8/4,000 |
| Standard_DS14 |16 |112 |224 |64 |64,000/512 (576) |51,200/512 |8/8,000 |

<sup>1</sup> DS シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../articles/virtual-machines/windows/premium-storage.md)」を参照してください。



## <a name="d-series"></a>D シリーズ 

ACU: 160

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

| サイズ         | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000/46/23                                           | 4 / 4x500                         | 2/500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8 / 8x500                         | 2/1,000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4/2,000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8/4,000                     |

<br>

## <a name="d-series---memory-optimized"></a>D シリーズ - メモリ最適化済み

ACU: 160

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

| サイズ         | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8 / 8x500                         | 2/1,000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4/2,000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8/4,000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64 / 64x500                       | 8/8,000                |

<br>

## <a name="a-series---compute-intensive-instances"></a>A シリーズ - コンピューティング集中型インスタンス

ACU: 225

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

A8 ～ A11 と H シリーズのサイズは、 *コンピューティング集中型インスタンス*とも呼ばれます。 これらのサイズを実行するハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。 A8 ～ A11 シリーズは Intel Xeon E5-2670 @ 2.6 GHZ を使用し、H シリーズは Intel Xeon E5-2667 v3 @ 3.2 GHz を使用します。  この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。 

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (HDD): GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC 数|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になり、超低待機時間と高帯域幅を実現します。

<br>

## <a name="a-series"></a>A シリーズ

ACU: 50 ～ 100

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (HDD): GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0.768 |20 |1 |1x500 |2/100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2/500  |
| Standard_A2 |2 |3.5 |135 |4 |4 x 500 |2/500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2/1,000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4/2,000 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2/500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2/1,000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4/2,000 |
<br>

<sup>1</sup> A0 サイズは、物理ハードウェアでオーバーサブスクライブされます。 この特定のサイズの場合のみ、他の顧客デプロイメントは、実行中のワークロードのパフォーマンスに影響することがあります。 下に、予想される基準として相対パフォーマンスを示していますが、約 15% の変動の可能性があります。

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 ～ A4 (CLI と PowerShell の使用)

クラシック デプロイ モデルでは、一部の VM サイズが CLI と PowerShell で若干異なります。

* Standard_A0: ExtraSmall 
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge

## <a name="basic-a"></a>Basic A

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

Basic レベルのサイズは主に、負荷分散や自動スケール、メモリ消費量の多い仮想マシンのいずれも必要としない用途 (開発ワークロードなど) 向けです。

|サイズ – サイズ\名前 | vCPU |メモリ|NIC (最大)|一時ディスクの最大サイズ |最大 データ ディスク数 (各ディスク 1,023 GB)|最大 IOPS (各ディスク 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1.75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|
 


