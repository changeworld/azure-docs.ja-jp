---
title: 'Azure Premium Storage: Windows VM におけるパフォーマンスのための設計 | Microsoft Docs'
description: Azure Premium SSD マネージド ディスクを使用する高パフォーマンスのアプリケーションを設計します。 Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "75371337"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: 高パフォーマンス用に設計する
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> ときには、ディスク パフォーマンスの問題のように見えるものが、実際にはネットワークのボトルネックであることもあります。 このような場合は、[ネットワーク パフォーマンス](../../virtual-network/virtual-network-optimize-network-bandwidth.md)を最適化する必要があります。
>
> ディスクのベンチマークを実行する場合は、「[ディスクのベンチマーク](disks-benchmarks.md)」に関する記事を参照してください。
>
> VM で高速ネットワークがサポートされる場合は、それが有効になっていることを確認する必要があります。 有効になっていない場合は、[Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) と [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) の両方で、既にデプロイされている VM 上で有効にすることができます。

Premium Storage を初めてご使用になる場合は、作業を始める前に、まず [IaaS VM 用の Azure ディスクの種類の選択](disks-types.md)に関する記事と、[Premium ページ BLOB ストレージ アカウントのスケーラビリティ ターゲット](../../storage/blobs/scalability-targets-premium-page-blobs.md)に関する記事をお読みください。

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

ディスクのベンチマークを実行する場合は、「[ディスクのベンチマーク](disks-benchmarks.md)」に関する記事を参照してください。

使用できるディスクの種類については、次のページを参照してください。[ディスクの種類の選択](disks-types.md)  

SQL Server ユーザーは、SQL Server のパフォーマンスのベスト プラクティスに関する次の記事をご覧ください。

* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage provides highest performance for SQL Server in Azure VM (Azure VM で SQL Server の最高レベルのパフォーマンスを実現する Azure Premium Storage)](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)