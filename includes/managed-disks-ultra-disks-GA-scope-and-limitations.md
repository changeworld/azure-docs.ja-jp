---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f485ef5d49ff2f0b9bfec4c6abc6db1b78c3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287396"
---
Ultra ディスクで現在利用できる結い位置のインフラストラクチャ冗長オプションは可用性ゾーンです。 他の冗長オプションを使用している VM では、Ultra ディスクを接続できません。

Ultra ディスクが利用できるリージョンとそれに該当する可用性オプションをまとめたものが次の表です。

> [!NOTE]
> 次の一覧のリージョンに、Ultra ディスクをサポートする可用性ゾーンがない場合、Ultra ディスクを接続するには、インフラストラクチャの冗長なしで、そのリージョンの VM をデプロイする必要があります。

| 冗長オプション | リージョン |
|--------------------|---------|
| **単一 VM** | オーストラリア中部<br/>ブラジル南部<br/>インド中部<br/>東アジア<br/>ドイツ中西部<br/>韓国中部<br/>米国中北部、米国中南部、米国西部<br/>US Gov アリゾナ、US Gov テキサス、US Gov バージニア |
| **2 つの可用性ゾーン** | フランス中部 |
| **3 つの可用性ゾーン** | オーストラリア東部<br/>カナダ中部<br/>北ヨーロッパ、西ヨーロッパ<br/>東日本<br/>東南アジア<br/>英国南部<br/>米国中部、米国東部、米国東部 2、米国西部 2 |

Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません。 次の表に、Ultra ディスクと互換性のある VM シリーズを示します。

|VM の種類     |サイズ    |説明  |
|------------|---------|-------------|
| 汎用|[DSv3 シリーズ](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)、[Ddsv4 シリーズ](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)、[Dsv4 シリーズ](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)、[Dasv4 シリーズ](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)| バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。|
| コンピューティング最適化|[Fsv2 シリーズ](../articles/virtual-machines/fsv2-series.md)| 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。|
| メモリ最適化|[DSv3 シリーズ](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)、[Dasv4 シリーズ](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)、[Edsv4 シリーズ](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)、[Esv4 シリーズ](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)、[M シリーズ](../articles/virtual-machines/m-series.md)、[Mv2 シリーズ](../articles/virtual-machines/mv2-series.md)|高いメモリ対 CPU 比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。
| ストレージ最適化|[LSv2 シリーズ](../articles/virtual-machines/lsv2-series.md)|ビッグ データ、SQL、NoSQL データベース、データ ウェアハウス、および大規模なトランザクション データベースに最適な、高いディスク スループットと IO。|
| GPU 最適化|[NCv2 シリーズ](../articles/virtual-machines/ncv2-series.md)、[NCv3 シリーズ](../articles/virtual-machines/ncv3-series.md)、[NCasT4_v3 シリーズ](../articles/virtual-machines/nct4-v3-series.md)、[ND シリーズ](../articles/virtual-machines/nd-series.md)、[NDv2 シリーズ](../articles/virtual-machines/ndv2-series.md)、[NVv3 シリーズ](../articles/virtual-machines/nvv3-series.md)、[NVv4 シリーズ](../articles/virtual-machines/nvv4-series.md)| 負荷の高いグラフィックスのレンダリングやビデオ編集、ディープ ラーニングを使用したモデル トレーニングと推論 (ND) に特化した仮想マシン。 1 つまたは複数の GPU で利用できます。|
| <nobr>パフォーマンス最適化</nobr> |[HB シリーズ](../articles/virtual-machines/hb-series.md)、[HC シリーズ](../articles/virtual-machines/hc-series.md)、[HBv2 シリーズ](../articles/virtual-machines/hbv2-series.md)|高スループットのネットワーク インターフェイス (RDMA) のオプションを備えた、最も高速かつ強力な CPU 仮想マシン。|

Ultra ディスクは、ディスク スナップショット、ディスク エクスポート、ディスク タイプの変更、VM イメージ、可用性セット、Azure Dedicated Host、Azure ディスク暗号化など、一部の機能では使用できません。 Azure Backup および Azure Site Recovery では、Ultra ディスクはサポートされていません。 さらに、キャッシュされない読み取りとキャッシュされない書き込みだけがサポートされます。

Ultra ディスクでは、既定で 4K の物理セクター サイズがサポートされます。 512E セクター サイズは一般提供されているオファリングとして利用できます (サインアップは不要です)。 ほとんどのアプリケーションは 4k のセクター サイズと互換性がありますが、512 バイトのセクター サイズが必要になる場合があります。 たとえば、Oracle Database では、4k のネイティブ ディスクをサポートするには、リリース 12.2 以降が必要です。 以前のバージョンの Oracle DB では、512 バイトのセクター サイズが必要となります。
