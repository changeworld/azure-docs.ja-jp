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
ms.openlocfilehash: 16e2a9cfbd9f08428fddade290117b27bc3401f7
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369365"
---
Azure H シリーズの仮想マシンは、バッチ処理、分析、分子モデリング、流体力学などのワークロードを処理することを目的とした、最新のハイ パフォーマンス コンピューティング VM です。 これらの 8 および 16 vCPU の VM は、DDR4 メモリに対応する Intel Haswell E5-2667 V3 プロセッサ テクノロジと SSD ベースの一時ストレージをベースに構築されます。 

H シリーズのラインナップは強力な CPU パワーに加えて、FDR InfiniBand を使用した低待機時間 RDMA ネットワークのためのさまざまなオプションと、複数のメモリ構成を備えており、メモリ集中型のコンピューティング要件にも対応しています。



## <a name="h-series"></a>H シリーズ

ACU: 290 ～ 300

Premium Storage:  サポートされていません

Premium Storage Caching:  サポートされていません

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大ディスク スループット: IOPS | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1,000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1,000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になり、超低待機時間と高帯域幅を実現します。

<br>






