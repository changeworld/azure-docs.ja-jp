---
title: マネージド ディスクのバースト
description: Azure ディスクのディスク バーストおよび Azure 仮想マシンのディスク バーストについて説明します。
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974330"
---
# <a name="disk-bursting"></a>ディスク バースト
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>仮想マシン レベルでのバースト
サポートされている次のサイズでは、パブリック クラウド内のすべてのリージョンで、VM レベルでのバーストのサポートが有効になっています。 
- [Lsv2 シリーズ](../lsv2-series.md)

サポートされている次のサイズについては、米国中西部でも VM レベルのバーストを利用できます。
- [Dsv3 シリーズ](../dv3-dsv3-series.md)
- [Esv3 シリーズ](../ev3-esv3-series.md)

バーストをサポートしている仮想マシンでは、バーストが既定で有効になっています。

## <a name="disk-level-bursting"></a>ディスク レベルでのバースト
バーストは、すべてのリージョンでサイズが P20 以下のディスクの場合に、[プレミアム SSD ](../disks-types.md#premium-ssd)上でも利用できます。 ディスク バースティングは、それをサポートするディスク サイズの新しいデプロイでは既定で有効になっています。 既存のディスク サイズでは、次のいずれかの方法を使用してバースティングを有効にすることができます (ディスク バースティングをサポートしている場合)。 
- **VM を再起動する** 
- **ディスクの接続を切断して、再接続する**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]