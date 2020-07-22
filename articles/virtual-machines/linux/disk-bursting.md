---
title: マネージド ディスクのバースト
description: Azure ディスクのディスク バーストおよび Azure 仮想マシンのディスク バーストについて説明します。
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295458"
---
# <a name="disk-bursting"></a>ディスク バースト
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>仮想マシン レベルでのバースト
サポートされている次のサイズでは、パブリック クラウド内のすべてのリージョンで、VM レベルでのバーストのサポートが有効になっています。 
- [Lsv2 シリーズ](../lsv2-series.md)

バーストをサポートしている仮想マシンでは、バーストが既定で有効になっています。

## <a name="disk-level-bursting"></a>ディスク レベルでのバースト
バーストは、Azure パブリック クラウド、Azure Government クラウド、Azure China クラウドのすべてのリージョンで、ディスク サイズ P20 以下の[プレミアム SSD](disks-types.md#premium-ssd) でも利用できます。 ディスク バースティングは、サポートされているディスク サイズのすべての新しいデプロイと既存のデプロイでは既定で有効になっています。 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
