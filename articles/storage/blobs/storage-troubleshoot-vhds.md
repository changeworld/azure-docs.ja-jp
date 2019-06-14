---
title: Azure VM に接続されたディスクのトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンの仮想ハード ディスク (VHD) 用のトラブルシューティング リソースへのリンクを提供します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150782"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure VM に接続されたディスクのトラブルシューティング 

Azure Virtual Machines (VM) は、OS ディスクおよび接続されたデータ ディスクに仮想ハード ディスク (VHD) を使用します。 VHD は、1 つ以上の Azure ストレージ アカウントにページ BLOB として格納されます。 この記事では、VHD で発生する可能性のある一般的な問題のトラブルシューティングに関するコンテンツを紹介します。 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>VM のストレージの削除エラーのトラブルシューティング

リソース マネージャーのデプロイ内の VM に VHD が接続されている場合、ストレージのリソースを削除するときにエラーが発生する可能性があります。 この問題を解決するには、次の記事のいずれかを参照してください。 

  * Linux VM 上: [Resource Manager デプロイメントでのストレージ削除エラー](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows VM 上: [Resource Manager デプロイメントでのストレージ削除エラー](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>接続された VHD による VM の予期しない再起動のトラブルシューティング

多数の VHD が接続されている VM で予期せず再起動が発生する場合、次のいずれかの記事を参照してください。

  * Linux VM 上: [接続された VHD による VM の予期しない再起動](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows VM 上: [接続された VHD による VM の予期しない再起動](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
