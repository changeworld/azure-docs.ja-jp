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
ms.openlocfilehash: ac8686d0ea5704492bfc2e08972a2f70c9b34c43
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37906821"
---
Azure H シリーズの仮想マシンは、分子モデリングや流体力学などのハイエンド コンピューティングのニーズを目的とした、最新のハイ パフォーマンス コンピューティング VM です。 これらの 8 および 16 vCPU の VM は、DDR4 メモリに対応する Intel Haswell E5-2667 V3 プロセッサ テクノロジと SSD ベースの一時ストレージをベースに構築されます。 

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






