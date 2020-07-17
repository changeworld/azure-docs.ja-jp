---
title: マネージド ディスクのバースト
description: Azure ディスクのディスク バーストおよび Azure 仮想マシンのディスク バーストについて説明します。
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594468"
---
# <a name="disk-bursting"></a>ディスク バースト
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>仮想マシン レベルでのバースト
サポートされている次のサイズでは、パブリック クラウド内のすべてのリージョンで、VM レベルでのバーストのサポートが有効になっています。 
- [Lsv2 シリーズ](../lsv2-series.md)

バーストをサポートしている仮想マシンでは、バーストが既定で有効になっています。

## <a name="disk-level-bursting"></a>ディスク レベルでのバースト
バーストは、すべてのリージョンでディスク サイズ P20 以下の[プレミアム SSD ](disks-types.md#premium-ssd)でも利用できます。 ディスク バースティングは、それをサポートするディスク サイズの新しいデプロイでは既定で有効になっています。 既存のディスク サイズでは、次のいずれかの方法を使用してバースティングを有効にすることができます (ディスク バースティングをサポートしている場合)。 
- **VM を再起動する** 
- **ディスクの接続を切断して、再接続する**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
