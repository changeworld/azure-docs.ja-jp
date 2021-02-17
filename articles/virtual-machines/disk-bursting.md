---
title: マネージド ディスクのバースト
description: Azure ディスクと Azure 仮想マシンのディスク バーストについて説明します。
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576195"
---
# <a name="managed-disk-bursting"></a>マネージド ディスクのバースト
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>仮想マシン レベルでのバースト
VM レベルでのバーストは、サポートされているすべてのリージョンの次の VM シリーズで有効になっています。
- [Lsv2 シリーズ](lsv2-series.md)
- [Dsv3 シリーズ](dv3-dsv3-series.md)
- [Esv3 シリーズ](ev3-esv3-series.md)

バーストをサポートしている仮想マシンでは、バーストが既定で有効になっています。

## <a name="disk-level-bursting"></a>ディスク レベルでのバースト
バーストは、Azure パブリック クラウド、Azure Government クラウド、Azure China クラウドのすべてのリージョンで、ディスク サイズ P20 以下の[プレミアム SSD](disks-types.md#premium-ssd) でも利用できます。 ディスク バースティングは、サポートされているディスク サイズのすべての新しいデプロイと既存のデプロイでは既定で有効になっています。 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
