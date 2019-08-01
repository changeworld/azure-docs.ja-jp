---
title: 'Azure Premium Storage: Windows VM におけるパフォーマンスのための設計 | Microsoft Docs'
description: Azure Premium Storage を使用する高パフォーマンスのアプリケーションを設計します。 Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 70da3509ce44fe260f8010ccf6eb1d2192ca6e73
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695498"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> ときには、ディスク パフォーマンスの問題のように見えるものが、実際にはネットワークのボトルネックであることもあります。 このような場合は、[ネットワーク パフォーマンス](../../virtual-network/virtual-network-optimize-network-bandwidth.md)を最適化する必要があります。
>
> ディスクのベンチマークを実行する場合は、「[ディスクのベンチマーク](disks-benchmarks.md)」に関する記事を参照してください。
>
> VM で高速ネットワークがサポートされる場合は、それが有効になっていることを確認する必要があります。 有効になっていない場合は、[Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) と [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) の両方で、既にデプロイされている VM 上で有効にすることができます。

Premium Storage を初めてご使用になる場合は、作業を始める前に、まず [IaaS VM 用の Azure ディスクの種類の選択](disks-types.md)に関する記事と 「[ストレージ アカウントでの Azure Storage のスケーラビリティとパフォーマンスのターゲット](../../storage/common/storage-scalability-targets.md)」をお読みください。


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
