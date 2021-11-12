---
title: Dedicated Host SKU 構成テーブル
description: ADH SKU の VM パッキングの仕様。
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 67b17324b550196728da62cb0e5b306d6387fa25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478006"
---
# <a name="azure-dedicated-host-sku-configuration-tables"></a>Azure Dedicated Host SKU 構成テーブル
Azure Dedicated Host SKU は、VM ファミリと特定のハードウェア仕様の組み合わせです。 デプロイできるのは、Dedicated Host SKU で指定されている VM シリーズのみです。 たとえば、Dsv3-Type3 では、[Dsv3 シリーズ](dv3-dsv3-series.md#dsv3-series)の VM のみをプロビジョニングできます。 

このドキュメントでは、すべての Dedicated Host SKU のハードウェア仕様と VM パッキングを紹介します。

## <a name="dasv4"></a>Dasv4
### <a name="dasv4-type1"></a>Dasv4-Type1
Dasv4-Type1 は、AMD の 2.35 GHz EPYC™ 7452 プロセッサを使用する Dedicated Host SKU です。 64 個の物理コア、96 個の vCPU、672 GiB の RAM を提供します。 Dasv4-Type1 は、[Dasv4 シリーズ](dav4-dasv4-series.md#dasv4-series) VM を実行します。 特定の VM パフォーマンス情報を理解するには、VM サイズに関するドキュメントを参照してください。

次のパッキング構成では、Dasv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]  | VM 数 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 24    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

Dasv4-Type1 では、複数の VM サイズを混在させることもできます。 Dasv4-Type1 での VM パッキングの組み合わせの例を次に示します。
- 1 D48asv4 + 3 D16asv4
- 1 D32asv4 + 2 D16asv4 + 8 D4asv4
- 20 D4asv4 + 8 D2asv4

## <a name="ddsv4"></a>Ddsv4
### <a name="ddsv4-type1"></a>Ddsv4-Type1
Ddsv4-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、68 個の vCPU、504 GiB の RAM を提供します。 Ddsv4-Type1 は、[Ddsv4 シリーズ](ddv4-ddsv4-series.md#ddsv4-series) VM を実行します。

次のパッキング構成では、Ddsv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]  | VM 数 |
|----------------|-----------------|---------------|----------|-------|
| 52             | 68              | 504 GiB       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 17    |
|                |                 |               | D8ds v4  | 8     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 1     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

Ddsv4-Type1 では、複数の VM サイズを混在させることもできます。 Ddsv4-Type1 での VM パッキングの組み合わせの例を次に示します。
- 1 D48dsv4 + 4 D4dsv4 + 2 D2dsv4
- 1 D32dsv4 + 2 D16dsv4 + 1 D4dsv4
- 10 D4dsv4 + 14 D2dsv4

## <a name="dsv4"></a>Dsv4
### <a name="dsv4-type1"></a>Dsv4-Type1

Dsv4-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、80 個の vCPU、504 GiB の RAM を提供します。 Dsv4-Type1 は、[Dsv4 シリーズ](dv4-dsv4-series.md#dsv4-series) VM を実行します。

次のパッキング構成では、Dsv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 20    |
|                |                 |               | D8s v4  | 10    |
|                |                 |               | D16s v4 | 5     |
|                |                 |               | D32s v4 | 2     |
|                |                 |               | D48s v4 | 1     |
|                |                 |               | D64s v4 | 1     |

Dsv4-Type1 では、複数の VM サイズを混在させることもできます。 Dsv4-Type1 での VM パッキングの組み合わせの例を次に示します。
- 1 D64sv4 + 1 D16sv4
- 1 D32sv4 + 2 D16dsv4 + 2 D8sv4
- 10 D4sv4 + 20 D2sv4

## <a name="dsv3"></a>Dsv3
### <a name="dsv3-type1"></a>Dsv3-Type1

Dsv3-Type1 は、Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4) プロセッサを使用する Dedicated Host SKU です。 40 個の物理コア、64 個の vCPU、および 256 GiB の RAM を提供します。 Dsv3-Type1 は、[Dsv3 シリーズ](dv3-dsv3-series.md#dsv3-series)の VM を実行します。

次のパッキング構成では、Dsv3-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 256 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 16    |
|                |                 |               | D8s v3  | 8     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Dsv3-Type1 では、複数の VM サイズを混在させることもできます。 Dsv3-Type1 での VM パッキングの組み合わせの例を次に示します。
- 1 D32sv3 + 1 D16sv3 + 1 D8sv3
- 1 D48sv3 + 3 D4sv3 + 2 D2sv3
- 10 D4sv3 + 12 D2sv3

### <a name="dsv3-type2"></a>Dsv3-Type2

Dsv3-Type2 は、Intel® Skylake (2.1 GHz Xeon® Platinum 8171M) プロセッサを利用する Dedicated Host SKU です。 48 個の物理コア、76 vCPU、および 504 GiB の RAM を提供します。 Dsv3-Type2 は、[Dsv3 シリーズ](dv3-dsv3-series.md#dsv3-series)の VM を実行します。

次のパッキング構成では、Dsv3-Type2 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 18    |
|                |                 |               | D8s v3  | 9     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Dsv3-Type2 では、複数の VM サイズを混在させることもできます。 Dsv3-Type2 での VM パッキングの組み合わせの例を次に示します。
- 1 D64sv3 + 2 D4vs3 + 2 D2sv3
- 1 D48sv3 + 4 D4sv3 + 6 D2sv3
- 12 D4sv3 + 14 D2sv3

### <a name="dsv3-type3"></a>Dsv3-Type3

Dsv3-Type3 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、80 個の vCPU、504 GiB の RAM を提供します。 Dsv3-Type3 は、[Dsv3 シリーズ](dv3-dsv3-series.md#dsv3-series) VM を実行します。

次のパッキング構成では、Dsv3-Type3 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 20    |
|                |                 |               | D8s v3  | 10    |
|                |                 |               | D16s v3 | 5     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

また、Dsv3 Type3 に複数の VM サイズを混在させることもできます。 Dsv3-Type3 での VM パッキングの組み合わせの例を次に示します。
- 1 D64sv3 + 1 D8sv3 + 2 D4sv3
- 1 D48sv3 + 1 D16sv3 + 4 D4sv3
- 15 D4sv3 +  10 D2sv3

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2-Type1

DCsv2-Type1 は、Intel® Coffee Lake (Xeon® E-2288G と SGX テクノロジ) プロセッサを利用する Dedicated Host SKU です。 8 個の物理コア、8 個の vCPU、64 GiB の RAM を提供します。 DCsv2-Type1 は、[DCsv2 シリーズ](dcv2-series.md) VM を実行します。

次のパッキング構成では、DCsv2-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64 GiB        | DC8s v2 | 1     |

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4-Type1

Easv4-Type1 は、AMD の 2.35 GHz EPYC™ 7452 プロセッサを使用する Dedicated Host SKU です。 64 個の物理コア、96 個の vCPU、672 GiB の RAM を提供します。 Easv4-Type1 は、[Easv4 シリーズ](eav4-easv4-series.md#easv4-series) VM を実行します。

次のパッキング構成では、Easv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]  | VM 数 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | E2as v4  | 32    |
|                |                 |               | E4as v4  | 21    |
|                |                 |               | E8as v4  | 10    |
|                |                 |               | E16as v4 | 5     |
|                |                 |               | E20as v4 | 4     |
|                |                 |               | E32as v4 | 2     |
|                |                 |               | E48as v4 | 1     |
|                |                 |               | E64as v4 | 1     |
|                |                 |               | E96as v4 | 1     |

## <a name="edsv4"></a>Edsv4
### <a name="edsv4-type1"></a>Edsv4-Type1

Edsv4-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、64 個の vCPU、504 GiB の RAM を提供します。 Edsv4-Type1 は、[Edsv4 シリーズ](edv4-edsv4-series.md#edsv4-series) VM を実行します。

次のパッキング構成では、Edsv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]  | VM 数 |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504 GiB       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4-Type1

Esv4-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、80 個の vCPU、504 GiB の RAM を提供します。 Esv4-Type1 は、[Esv4 シリーズ](ev4-esv4-series.md#esv4-series) VM を実行します。

次のパッキング構成では、Esv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3-Type1

Esv3-Type1 は、Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4) プロセッサを使用する Dedicated Host SKU です。 40 個の物理コア、64 個の vCPU、448 GiB の RAM を提供します。 Esv3-Type1 は、[Esv3 シリーズ](ev3-esv3-series.md#ev3-series) VM を実行します。

次のパッキング構成では、Esv3-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 448 GiB       | E2s v3  | 28    |
|                |                 |               | E4s v3  | 14    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 2     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type2"></a>Esv3-Type2

Esv3-Type2 は、Intel® Skylake (Xeon® 8171M) プロセッサを利用する Dedicated Host SKU です。 48 個の物理コア、78 個の vCPU、504 GiB の RAM を提供します。 Esv3-Type2 は、[Esv3 シリーズ](ev3-esv3-series.md#ev3-series) VM を実行します。

次のパッキング構成では、Esv3-Type2 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type3"></a>Esv3-Type3

Esv3-Type3 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、80 個の vCPU、504 GiB の RAM を提供します。 Esv3-Type3 は、[Esv3 シリーズ](ev3-esv3-series.md#ev3-series) VM を実行します。

次のパッキング構成では、Esv3-Type3 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

## <a name="fsv2"></a>Fsv2
### <a name="fsv2-type2"></a>Fsv2-Type2

Fsv2-Type2 は、Intel® Skylake (Xeon® Platinum 8168) プロセッサを利用する Dedicated Host SKU です。 48 個の物理コア、72 個の vCPU、144 GiB の RAM を提供します。 Fsv2-Type2 は、[Fsv2 シリーズ](fsv2-series.md) VM を実行します。

次のパッキング構成では、Fsv2-Type2 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 72              | 144 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 18    |
|                |                 |               | F8s v2  | 9     |
|                |                 |               | F16s v2 | 4     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type3"></a>Fsv2-Type3

Fsv2-Type3 は、Intel® Cascade Lake (Xeon® Platinum 8272CL) プロセッサを使用する Dedicated Host SKU です。 52 個の物理コア、86 個の vCPU、504 GiB の RAM を提供します。 Fsv2-Type3 は、[Fsv2 シリーズ](fsv2-series.md) VM を実行します。

次のパッキング構成では、Fsv2-Type3 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 86              | 504 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 21    |
|                |                 |               | F8s v2  | 10    |
|                |                 |               | F16s v2 | 5     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds-Type1

FXmds-Type1 は、Intel® Cascade Lake (Xeon® Gold 6246R) プロセッサを使用する Dedicated Host SKU です。 32 個の物理コア、48 個の vCPU、1,152 GiB の RAM を提供します。 FXmds-Type1 は、[FX シリーズ](fx-series.md)の VM を実行します。

次のパッキング構成では、FXmds-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1,152 GiB     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2-Type1

Lsv2-Type1 は、Intel® Cascade Lake (Xeon® Gold 6246R) プロセッサを使用する Dedicated Host SKU です。 64 個の物理コア、80 個の vCPU、640 GiB の RAM を提供します。 Lsv2-Type1 は、[Lsv2 シリーズ](lsv2-series.md)の VM を実行します。

次のパッキング構成では、Lsv2-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 GiB       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="m"></a>M
### <a name="ms-type1"></a>Ms-Type1

Ms-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを使用する Dedicated Host SKU です。 112 個の物理コア、128 個の vCPU、2048 GiB の RAM を提供します。 Ms-Type1 は、M、Mls、Ms、Mts VM を含む [M シリーズ](m-series.md)の VM を実行します。

次のパッキング構成では、Ms-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ] | VM 数 |
|----------------|-----------------|---------------|---------|-------|
| 112            | 128             | 2,048 GiB     | M32ls   | 4     |
|                |                 |               | M32ts   | 4     |
|                |                 |               | M64     | 2     | 
|                |                 |               | M64s    | 2     |
|                |                 |               | M64ls   | 2     |
|                |                 |               | M128    | 1     | 
|                |                 |               | M128s   | 1     |

### <a name="msm-type1"></a>Msm-Type1

Msm-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを使用する Dedicated Host SKU です。 112 個の物理コア、128 個の vCPU、3892 GiB の RAM を提供します。 Msm-Type1 は、Ms、Mms、Mts、Mls VM を含む [M シリーズ](m-series.md)の VM を実行します。

次のパッキング構成では、Msm-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]   | VM 数 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 128             | 3,892 GiB     | M8ms      | 16    |
|                |                 |               | M8-2ms    | 16    |
|                |                 |               | M8-4ms    | 16    |
|                |                 |               | M16ms     | 8     |
|                |                 |               | M16-4ms   | 8     |
|                |                 |               | M16-8ms   | 8     |
|                |                 |               | M32ts     | 4     |
|                |                 |               | M32ls     | 4     |
|                |                 |               | M32ms     | 4     |
|                |                 |               | M32-8ms   | 4     |
|                |                 |               | M32-16ms  | 4     |
|                |                 |               | M64ms     | 2     |
|                |                 |               | M64       | 2     | 
|                |                 |               | M64s      | 2     |
|                |                 |               | M64m      | 2     |
|                |                 |               | M64ls     | 2     |
|                |                 |               | M64-16ms  | 2     |
|                |                 |               | M64-32ms  | 2     |
|                |                 |               | M128ms    | 1     |
|                |                 |               | M128s     | 1     |
|                |                 |               | M128m     | 1     |
|                |                 |               | M128      | 1     | 
|                |                 |               | M128-32ms | 1     |
|                |                 |               | M128-64ms | 1     |

## <a name="mv2"></a>Mv2
### <a name="msmv2-type1"></a>Msmv2-Type1

Msm-Type1 は、Intel® Skylake (Xeon® Platinum 8180M) プロセッサを利用する Dedicated Host SKU です。 224 個の物理コア、416 個の vCPU、11400 GiB の RAM を提供します。 Msmv2-Type1 は、Msv2、Mmsv2 VM を含む [Mv2 シリーズ](mv2-series.md)の VM を実行します。

次のパッキング構成では、Msm-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]       | VM 数 |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 11,400 GiB    | M208ms v2     | 2     |
|                |                 |               | M208s  v2     | 2     |
|                |                 |               | M416-208ms v2 | 1     | 
|                |                 |               | M416-208s v2  | 1     | 
|                |                 |               | M416ms v2     | 1     |  
|                |                 |               | M416s v2      | 1     |  

### <a name="msv2-type1"></a>Msv2-Type1

Msv2-Type1 は、Intel® Skylake (Xeon® Platinum 8180M) プロセッサを利用する Dedicated Host SKU です。 224 個の物理コア、416 個の vCPU、5700 GiB の RAM を提供します。 Msv2-Type1 は、Msv2、Mmsv2 VM を含む [Mv2 シリーズ](mv2-series.md)の VM を実行します。

次のパッキング構成では、Msv2-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]       | VM 数 |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 5,700 GiB     | M208ms v2     | 2     |
|                |                 |               | M208s v2      | 1     |
|                |                 |               | M416-208s v2  | 1     |
|                |                 |               | M416s v2      | 1     |

## <a name="msv2"></a>Msv2
### <a name="mmsv2medmem-type1"></a>Mmsv2MedMem-Type1
Mmsv2MedMem-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを利用する Dedicated Host SKU です。 112 個の物理コア、192 個の vCPU、4096 GiB の RAM を提供します。 Mmsv2MedMem-Type1 は、Msv2、Mmsv2 VM を含む [Msv2 シリーズ](msv2-mdsv2-series.md)の VM を実行します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]   | VM 数 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 4,096 GiB     | M32ms v2  | 4     |
|                |                 |               | M64s v2   | 3     |
|                |                 |               | M64ms v2  | 2     |
|                |                 |               | M128ms v2 | 1     |
|                |                 |               | M128s v2  | 1     |

### <a name="msv2medmem-type1"></a>Msv2MedMem-Type1
Msv2MedMem-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを利用する Dedicated Host SKU です。 112 個の物理コア、192 個の vCPU、2048 GiB の RAM を提供します。 Msv2MedMem-Type1 は、Msv2、Mmsv2 VM を含む [Msv2 シリーズ](msv2-mdsv2-series.md)の VM を実行します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]    | VM 数 |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 2,048 GiB     | M32ms v2   | 2     |
|                |                 |               | M64s v2    | 2     |
|                |                 |               | M64ms v2   | 1     |
|                |                 |               | M128s v2   | 1     |

## <a name="mdsv2"></a>Mdsv2
### <a name="mdmsv2medmem-type1"></a>Mdmsv2MedMem-Type1
Mdmsv2MedMem-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを利用する Dedicated Host SKU です。 112 個の物理コア、192 個の vCPU、4096 GiB の RAM を提供します。 Mdmsv2MedMem-Type1 は、Mdsv2、Mdmsv2 VM を含む [Msv2 シリーズ](msv2-mdsv2-series.md)の VM を実行します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]    | VM 数 |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 4,096 GiB     | M32dms v2  | 4     |
|                |                 |               | M64ds v2   | 2     |
|                |                 |               | M64dms v2  | 2     |
|                |                 |               | M128ds v2  | 1     |
|                |                 |               | M128dms v2 | 1     |

### <a name="mdsv2medmem-type1"></a>Mdsv2MedMem-Type1
Mdsv2MedMem-Type1 は、Intel® Cascade Lake (Xeon® Platinum 8280) プロセッサを利用する Dedicated Host SKU です。 112 個の物理コア、192 個の vCPU、2048 GiB の RAM を提供します。 Mdsv2MedMem-Type1 は、Mdsv2、Mdmsv2 VM を含む [Msv2 シリーズ](msv2-mdsv2-series.md)の VM を実行します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]   | VM 数 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 2,048 GiB     | M32dms v2 | 2     |
|                |                 |               | M64ds v2  | 2     |
|                |                 |               | M64dms v2 | 1     |
|                |                 |               | M128ds v2 | 1     |

## <a name="nvasv4"></a>NVasv4
### <a name="nvasv4-type1"></a>NVasv4-Type1

NVasv4-Type1 は、AMD Radeon Instinct MI25 GPU を備えた AMD® Rome (EPYC™ 7V12) プロセッサを利用する Dedicated Host SKU です。 128 個の物理コア、128 個の vCPU、448 GiB の RAM を提供します。 NVasv4-Type1 は、[NVsv4 シリーズ](nvv4-series.md) VM を実行します。

次のパッキング構成では、NVasv4-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]   | VM 数 |
|----------------|-----------------|---------------|-----------|-------|
| 128            | 128             | 448 GiB       | NV4as v4  | 30    |
|                |                 |               | NV8as v4  | 15    |
|                |                 |               | NV16as v4 | 7     |
|                |                 |               | NV32as v4 | 3     |

## <a name="nvsv3"></a>NVsv3
### <a name="nvsv3-type1"></a>NVsv3-Type1

NVsv3-Type1 は、NVDIDIA Tesla M60 GPU および NVIDIA GRID テクノロジを備えた Intel® Broadwell (E5-2690 v4) プロセッサを利用する Dedicated Host SKU です。 28 個の物理コア、48 個の vCPU、448 GiB の RAM を提供します。 NVsv3-Type1 は、[NVv3 シリーズ](nvv3-series.md) VM を実行します。

次のパッキング構成では、NVsv3-Type1 ホストに追加できる統一された VM の最大パッキングの概要を示します。

| 物理コア | 使用可能な vCPU | 使用可能な RAM | [VM サイズ]  | VM 数 |
|----------------|-----------------|---------------|----------|-------|
| 28             | 48              | 448 GiB       | NV12s v3 | 4     |
|                |                 |               | NV24s v3 | 2     |
|                |                 |               | NV48s v3 | 1     | 


## <a name="next-steps"></a>次のステップ

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。

- リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用する、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)で使用できるサンプル テンプレートがあります。