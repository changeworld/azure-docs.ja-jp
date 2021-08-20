---
title: vNet でのディスク プール計画に関する注意事項
description: Azure VMware Solution ホストの近くに vNet をデプロイすることの重要性に関する重要な注意事項。
ms.topic: include
ms.date: 07/14/2021
ms.openlocfilehash: 39bcf823a71852396c1c05db4902abf000433499
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640590"
---
[Azure ディスク プール](../../virtual-machines/disks-pools.md)を使用して Azure VMware Solution ホストをスケーリングする場合は、ExpressRoute 仮想ネットワーク ゲートウェイを使用してホストの近くに vNet をデプロイすることが重要です。  ストレージがホストに近いほど、パフォーマンスが向上します。