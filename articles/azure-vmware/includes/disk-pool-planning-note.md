---
title: vNet でのディスク プール計画に関する注意事項
description: Azure VMware Solution ホストの近くに vNet をデプロイすることの重要性に関する重要な注意事項。
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729101"
---
[Azure ディスク プール](../../virtual-machines/disks-pools.md)を使用して Azure VMware Solution ホストをスケーリングする場合は、ExpressRoute 仮想ネットワーク ゲートウェイを使用してホストの近くに vNet をデプロイすることが重要です。 ストレージがホストに近いほど、パフォーマンスが向上します。
