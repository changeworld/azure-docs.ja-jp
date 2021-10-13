---
title: vNet でのディスク プール計画に関する注意事項
description: Azure VMware Solution ホストの近くに vNet をデプロイすることの重要性に関する重要な注意事項。
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638414"
---
[Azure ディスク プール](../../virtual-machines/disks-pools.md)を使用して Azure VMware Solution ホストをスケーリングする場合は、ExpressRoute 仮想ネットワーク ゲートウェイを使用してホストの近くに vNet をデプロイすることが重要です。 ストレージがホストに近いほど、パフォーマンスが向上します。
