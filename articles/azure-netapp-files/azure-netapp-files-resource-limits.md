---
title: Azure NetApp Files のリソース制限 | Microsoft Docs
description: 容量プール、ボリューム、委任されたサブネットの制限など、Azure NetApp Files のリソース制限について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056520"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限
Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

## <a name="capacity_pools"></a>容量プール

- 1 つの容量プールの最小サイズは 4 TiB で、最大サイズは 500 TiB です。 
- 各容量プールが属することができる NetApp アカウントは 1 つだけです。 ただし、1 つの NetApp アカウント内に複数の容量プールを持つことができます。  

## <a name="volumes"></a>ボリューム

- 1 つのボリュームの最小サイズは 100 GiB で、最大サイズは 92 TiB です。
- 各リージョンで Azure サブスクリプションごとに最大 100 個のボリュームを持つことができます。  

## <a name="delegated_subnet"></a>委任されたサブネット 

各 Azure 仮想ネットワーク (VNet) で、1 つのサブネットだけを Azure NetApp Files に委任できます。

## <a name="next-steps"></a>次の手順

[Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
