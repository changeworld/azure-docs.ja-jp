---
title: マネージド ディスクのバースト
description: Azure ディスクのディスク バーストおよび Azure 仮想マシンのディスク バーストについて説明します。
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650946"
---
# <a name="disk-bursting"></a>ディスク バースト
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>仮想マシン レベルでのバースト
サポートされている次のサイズでは、パブリック クラウド内のすべてのリージョンで、VM レベルでのバーストのサポートが有効になっています。 
- [Lsv2 シリーズ](../lsv2-series.md)

バーストをサポートしている仮想マシンでは、バーストが既定で有効になっています。

## <a name="disk-level-bursting"></a>ディスク レベルでのバースト
バーストは、Azure パブリック クラウド、Azure Government クラウド、Azure China クラウドのすべてのリージョンで、ディスク サイズ P20 以下の[プレミアム SSD](disks-types.md#premium-ssd) でも利用できます。 ディスク バースティングは、それをサポートするディスク サイズの新しいデプロイでは既定で有効になっています。 既存のディスク サイズでは、次のいずれかの方法を使用してバースティングを有効にすることができます (ディスク バースティングをサポートしている場合)。 
- **VM を再起動する** 
- **ディスクの接続を切断して、再接続する**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
