---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471701"
---
現時点では、共有ディスクを有効にできるのは Premium SSD だけです。 この機能をサポートするディスクサイズは P15 以上です。 ディスクのサイズによって `maxShares` 制限が異なる場合があります。これは、`maxShares` 値を設定するときに超えることはできません。

ディスクごとに、ディスクを同時に共有できるノードの最大数を表す `maxShares` 値を定義できます。 たとえば、2 ノードのフェールオーバークラスターをセットアップする場合は、`maxShares=2`を設定します。 最大値は上限です。 ノード数が指定された `maxShares` 値よりも少ない場合は、ノードがクラスターに参加したり、クラスターから離脱したり (ディスクのマウントまたはマウント解除) できます。

> [!NOTE]
> `maxShares` 値を設定または編集できるのは、ディスクがすべてのノードからデタッチされている場合のみです。

次の表は、ディスクサイズによって `maxShares` に許容される最大値を示しています。

|ディスク サイズ  |maxShares の制限  |
|---------|---------|
|P15、P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

ディスクの IOPS と帯域幅の制限は、`maxShares` 値の影響を受けません。 たとえば、P15 ディスクの最大 IOPS は、maxShares = 1 または maxShares > 1 のいずれでも1100です。