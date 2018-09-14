---
title: Azure VM に接続されたディスクのトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンの仮想ハード ディスク (VHD) 用のトラブルシューティング リソースへのリンクを提供します。
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 81db3a819c28aab8f4b644a940eeb5e6c5ecf3d6
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307174"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure VM に接続されたディスクのトラブルシューティング 

Azure Virtual Machines (VM) は、OS ディスクおよび接続されたデータ ディスクに仮想ハード ディスク (VHD) を使用します。 VHD は、1 つ以上の Azure ストレージ アカウントにページ BLOB として格納されます。 この記事では、VHD で発生する可能性のある一般的な問題のトラブルシューティングに関するコンテンツを紹介します。 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>VM のストレージの削除エラーのトラブルシューティング

リソース マネージャーのデプロイ内の VM に VHD が接続されている場合、ストレージのリソースを削除するときにエラーが発生する可能性があります。 この問題を解決するには、次の記事のいずれかを参照してください。 

  * Linux 上の VM の場合: [Resource Manager デプロイでのストレージ削除エラー](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows 上の VM の場合: [Resource Manager デプロイでのストレージ削除エラー](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>接続された VHD による VM の予期しない再起動のトラブルシューティング

多数の VHD が接続されている VM で予期せず再起動が発生する場合、次のいずれかの記事を参照してください。

  * Linux 上の VM の場合: [接続された VHD による VM の予期しない再起動](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows 上の VM の場合: [接続された VHD による VM の予期しない再起動](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
