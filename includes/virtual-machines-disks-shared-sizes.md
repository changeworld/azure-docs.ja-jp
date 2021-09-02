---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d7d9389e00b3b0f23d590563a242ae0c12e2463
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740101"
---
現状では、Ultra ディスクと Premium SSD のみで共有ディスクを有効にできます。 ディスクのサイズによって `maxShares` 制限が異なる場合があります。これは、`maxShares` 値を設定するときに超えることはできません。 Premium SSD の場合、ディスク共有をサポートするディスク サイズは P15 以上です。

ディスクごとに、ディスクを同時に共有できるノードの最大数を表す `maxShares` 値を定義できます。 たとえば、2 ノードのフェールオーバークラスターをセットアップする場合は、`maxShares=2`を設定します。 最大値は上限です。 ノード数が指定された `maxShares` 値よりも少ない場合は、ノードがクラスターに参加したり、クラスターから離脱したり (ディスクのマウントまたはマウント解除) できます。

> [!NOTE]
> `maxShares` 値を設定または編集できるのは、ディスクがすべてのノードからデタッチされている場合のみです。

### <a name="premium-ssd-ranges"></a>Premium SSD の範囲

次の表は、`maxShares` の許容最大値を Premium SSD サイズ別にまとめたものです。

|ディスク サイズ  |maxShares の制限  |
|---------|---------|
|P1、P2、P3、P4、P6、P10、P15、P20     |3         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

ディスクの IOPS と帯域幅の制限は、`maxShares` 値の影響を受けません。 たとえば、P15 ディスクの最大 IOPS は、maxShares = 1 でも maxShares > 1 でも 1100 です。

### <a name="standard-ssd-ranges"></a>Standard SSD の範囲

次の表は、`maxShares` の許容最大値を Standard SSD サイズ別にまとめたものです。

|ディスク サイズ  |maxShares の制限  |
|---------|---------|
|E1、E2、E3、E4、E6、E10、E15、E20     |3         |
|E30、E40、E50     |5         |
|E60、E70、E80     |10         |

ディスクの IOPS と帯域幅の制限は、`maxShares` 値の影響を受けません。 たとえば、E15 ディスクの最大 IOPS は、maxShares = 1 でも maxShares > 1 でも 500 です。

### <a name="ultra-disk-ranges"></a>Ultra ディスクの範囲

最小 `maxShares` 値は 1、最大 `maxShares` 値は 5 です。 Ultra ディスクにサイズ制限はありません。Ultra ディスクではサイズを問わず、`maxShares` に最大値までのあらゆる値を使用できます。