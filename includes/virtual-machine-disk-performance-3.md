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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016548"
---
![[メトリック] メニュー](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

ただし、Standard_D8s_v3 が達成できるのは合計で 28,600 の IOPS です。メトリックを使用すると、何が起こっているかを調べ、ストレージ IO のボトルネックを特定することができます。 まず、左側のメニューの [メトリック] ボタンを探し選択します。

![[メトリック] メニュー](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

最初に **[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)** メトリックを見てみましょう。

![[VM Cached IOPS Consumed Percentage]\(VM のキャッシュされた IOPS の消費率\)](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

このメトリックは、VM のキャッシュされた IOPS に対して割り当てられた 16,000 IOPS の 61% が使用されていることを示しています。 100% ではないため、これからはストレージ IO のボトルネックがキャッシュされたディスクを原因とするものではないことがわかります。 では、 **[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)** メトリックを見てみましょう。

![[VM Uncached IOPS Consumed Percentage]\(VM のキャッシュされていない IOPS の消費率\)](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

このメトリックは 100% であり、VM のキャッシュされていない IOPS に対して割り当てられた 12,800 のすべての IOPS が消費されていることを示しています。 これを修復する方法の 1 つには、追加の IO を処理できるより大きなサイズに VM のサイズを変更する方法があります。 ただし、これを行う前に、接続されているディスクで認識されている IOPS の数を確認してみる必要があります。 まずは OS ディスクを、 **[OS Disk IOPS Consumed Percentage]** \(OS ディスク IOPS の消費率\) で確認してみましょう。

![[OS Disk IOPS Consumed Percentage]\(OS ディスク IOPS の消費率\)](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

このメトリックは、この P30 OS ディスクにプロビジョニングされている 5,000 の IOPS のうち、約 90% が使用されていることを示しています。 これは、OS ディスクにボトルネックがないことを意味しています。 次に、VM に接続されているデータ ディスクを **[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)** で確認してみましょう。

![[Data Disk IOPS Consumed Percentage]\(データ ディスク IOPS の消費率\)](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

このメトリックでは、接続されているすべてのディスクで消費される平均 IOPS は約 42% であることを示しています。 この割合は、ディスクによって使用され、ホスト キャッシュで処理されない IOPS に基づいて計算されています。 これらのメトリックに**分割**を適用して LUN 値で分割して、このメトリックについて細かく確認してみましょう。

![[Data Disk IOPS Consumed Percentage With Splitting]\(分割を使用したデータ ディスク IOPS の消費率\)](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

このメトリックからは、LUN 3 と 2 に接続されているデータ ディスクで、プロビジョニングされた IOPS の約 85% が使用されていることがわかります。 次の VM とディスクのアーキテクチャ図では、IO がどのようになっているかを示しています。

![ストレージ IO メトリックの例の図](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
