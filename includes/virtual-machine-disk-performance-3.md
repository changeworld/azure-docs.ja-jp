---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518071"
---
![r = 22.8k が強調表示されている fio の出力のスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 は、合計 28,600 IOPS を達成できます。 メトリックを使用して、何が起こっているのかを調べ、ストレージ IO のボトルネックを特定しましょう。 左側のペインで、 **[メトリック]** を選択します。

![左側のペインの強調表示された [メトリック] を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

最初に **[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)** メトリックを見てみましょう。

![[VM のキャッシュされた IOPS の消費率] を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

このメトリックは、VM 上のキャッシュされた IOPS に割り当てられている 16,000 IOPS のうち 61% が使用されていることを示しています。 この割合は、100% でないことから、ストレージ IO のボトルネックは、キャッシュされているディスクではないことを意味します。 では、 **[VM のキャッシュされていない IOPS の消費率]** メトリックを見てみましょう。

![[VM のキャッシュされていない IOPS の消費率] を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

このメトリックは 100% です。 これは、VM 上のキャッシュされていない IOPS に割り当てられた 12,800 IOPS のすべてが使用されていることを示しています。 この問題を解決する方法の 1 つには、追加の IO を処理できるより大きなサイズに VM のサイズを変更する方法があります。 ただし、その前に、アタッチされているディスクを調べて、表示されている IOPS の数を確認しましょう。 **[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)** を見て、OS ディスクを確認します。

![[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\) を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

このメトリックは、この P30 OS ディスクにプロビジョニングされた 5,000 IOPS のうち約 90% が使用されていることを示しています。 この割合は、ボトルネックは OS ディスクではないことを意味します。 次に、VM にアタッチされているデータ ディスクを **[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)** で確認してみましょう。

![[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\) を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

このメトリックは、アタッチされているすべてのディスクで使用される平均 IOPS が約 42% であることを示しています。 この割合は、ディスクによって使用され、ホスト キャッシュで処理されない IOPS に基づいて計算されます。 これらのメトリックに *分割* を適用して LUN 値で分割して、このメトリックについて細かく確認してみましょう。

![分割を適用した [Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\) を示すスクリーンショット。](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

このメトリックは、LUN 3 と 2 にアタッチされているデータ ディスクで、プロビジョニングされた IOPS の約 85% が使用されていることを示します。 VM およびディスクのアーキテクチャの観点から見た IO の図を次に示します。

![ストレージ IO メトリックの例の図。](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
