---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663363"
---
この記事は、ディスクのパフォーマンスと、Azure Virtual Machines と Azure ディスクを組み合わせたときの動作のしくみを明確にするために役立ちます。 ディスク IO のボトルネックを診断する方法と、パフォーマンスを最適化するために行うことができる変更についても説明します。

## <a name="how-does-disk-performance-work"></a>ディスクのパフォーマンス動作のしくみ
Azure 仮想マシンには、仮想マシンの種類とサイズに基づく IOPS とスループットのパフォーマンス制限があります。 仮想マシンに接続できる OS ディスクとデータ ディスクには、独自の IOP とスループットの制限があります。 仮想マシンで実行されているアプリケーションで、仮想マシンまたは接続されているディスクに割り当てられているものを超える IOPS またはスループットを要求した場合、アプリケーションのパフォーマンスは上限に達します。 これが発生すると、アプリケーションのパフォーマンスが最適化されなくなり、待機時間の増加などの悪影響が生じる可能性があります。 これを説明するために、いくつかの例を見てみましょう。 これらの例を容易に理解できるようにするため、IOP のみに注目しますが、同じ論理がスループットにも適用されます。

## <a name="disk-io-capping"></a>ディスク IO の上限
設定: 
- Standard_D8s_v3 
    - キャッシュされない IOPS:12,800
- E30 OS ディスク
    - IOPS:500 
- 2 台の E30 データ ディスク
    - IOPS:500

![ディスク レベルの上限](media/vm-disk-performance/disk-level-throttling.jpg)

仮想マシンで実行されるアプリケーションで、仮想マシンに 10,000 IOP を必要とする要求を行います。 Standard_D8s_v3 仮想マシンは最大 12,800 IOP を実行できるため、VM でそのすべてが可能です。 10,000 IOP 要求は、異なるディスクに対する 3 つの異なる要求に分割されます。 1,000 IOP がオペレーティング システム ディスクに要求され、各データ ディスクに 4,500 IOP が要求されます。 E30 ディスクに接続されているすべてのディスクは 500 IOP しか処理できないため、それぞれ 500 IOP で応答します。 接続されたディスクによってアプリケーションのパフォーマンスが制限され、1,500 IOP しか処理できません。 Premium SSD P30 ディスクのようなパフォーマンスが高いディスクを使用した場合、10,000 IOPS のピーク パフォーマンスで動作する可能性があります。

## <a name="virtual-machine-io-capping"></a>仮想マシンの IO 上限
設定: 
- Standard_D8s_v3 
    - キャッシュされない IOPS:12,800
- P30 OS ディスク
    - IOPS:5,000 
- 2 台の P30 データ ディスク 
    - IOPS:5,000

![仮想マシン レベルの上限](media/vm-disk-performance/vm-level-throttling.jpg)

仮想マシンで実行されるアプリケーションで、15,000 IOP を必要とする要求を行います。 残念ながら、Standard_D8s_v3 仮想マシンは 12,800 IOP を処理するようにのみプロビジョニングされています。 ここから、アプリケーションで仮想マシンの制限による上限が発生し、割り当て済みの 12,800 IOP を割り当てる必要があります。 要求された 12,800 IOP は、異なるディスクに対する 3 つの異なる要求に分割されます。 オペレーティング システム ディスクに 4,267 IOP が要求され、各データ ディスクに 4,266 IOP が要求されます。 接続されているディスクはすべて 5,000 IOP を処理できる P30 ディスクであるため、要求された量で応答します。