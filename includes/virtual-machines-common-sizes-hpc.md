---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 006a44e42ed209b6f0e614b92f97e43ec30b99ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469230"
---
Azure HPC 最適化済み仮想マシン (VM) は、実環境のさまざまなアプリケーションに対して、リーダー クラスのパフォーマンス、MPI スケーラビリティ、およびコスト効率を提供するように設計されています。
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>大規模 HPC 向けの InfiniBand ネットワーク
HBv2 VM は 200 Gb/秒の Mellanox HDR InfiniBand を、HB VM と HC VM は、どちらも 100 Gb/秒 の Mellanox EDR InfiniBand を備えています。 これらの VM の種類は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。

HBv2 VM では、アダプティブ ルーティングと動的接続トランスポート (DCT、標準 RC トランスポートと UD トランスポートに加えて) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、それらを使用することを強くお勧めします。  

HBv2、HB、HC の各 VM では、標準 Mellanox/OFED ドライバーがサポートされます。 そのため、すべての RDMA の動詞と MPI の型がサポートされます。 これらの VM の種類では、アプリケーションのパフォーマンスを向上させるために、MPI コレクティブのハードウェアベースのオフロードもサポートされています。
 
初代の H シリーズ VM は、56 Gb/秒 の Mellanox FDR InfiniBand を備えています。 H シリーズの InfiniBand インターフェイスを活用するには、お客様は、Azure Marketplace のこの VM の種類向けに正式にサポートされている固有のイメージを使用してデプロイする必要があります。 


## <a name="hbv2-series"></a>HBv2 シリーズ
HBv2 シリーズ VM は、流体力学、有限要素解析、貯留層シミュレーションなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 120 個の AMD EPYC 7742 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 各 HBv2 VM では、最大 340 GB/秒のメモリ帯域幅が提供され、最大 4 テラフロップの FP64 コンピューティングが提供されます。 

Premium Storage: サポートされています

| Size | vCPU | プロセッサ | メモリ (GB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | All | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB シリーズ
HB シリーズ VM は、流体力学、陽解法有限要素解析、気象モデリングなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 60 個の AMD EPYC 7551 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 HB VM では、最大 260 GB/秒のメモリ帯域幅が提供されます。  

ACU: 199-216

Premium Storage: サポートされています

Premium Storage Caching: サポートされています

| Size | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GiB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GiB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC シリーズ
HC シリーズ VM は、陰解法有限要素解析、分子力学、計算化学などの、高密度計算に基づいたアプリケーション向けに最適化されています。 HC VM は 44 個の Intel Xeon Platinum 8168 プロセッサ コア、CPU コアあたり 8 GB の RAM を搭載し、ハイパースレッディングはありません。 Intel Xeon Platinum プラットフォームでは、Intel Math Kernel Library などの Intel の豊富なソフトウェア ツールのエコシステムがサポートされています。 

ACU: 297-315

Premium Storage: サポートされています

Premium Storage Caching: サポートされています


| Size | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GiB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GiB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H シリーズ
H シリーズ VM は、高い CPU 周波数またはコアあたり大容量メモリの要件に基づいたアプリケーション向けに最適化されています。 H シリーズ VM は、8 個または 16 個の Intel Xeon E5 2667 v3 プロセッサ コア、CPU コアあたり最大 14 GB の RAM を備え、ハイパースレッディングはありません。 H シリーズ VM では、最大 80 GB/秒のメモリ帯域幅が提供されます。

ACU: 290-300

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

| Size | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GiB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GiB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になります。

<br>
