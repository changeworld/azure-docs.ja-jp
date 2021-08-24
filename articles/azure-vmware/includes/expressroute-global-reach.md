---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070746"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 Microsoft Enterprise Edge (MSEE) レベルで回線を直接接続します。そのため、この接続には、サブスクリプション内にオンプレミスへの ExpressRoute 回線がある仮想ネットワークが必要です。  その理由は、vNet ゲートウェイ (ExpressRoute ゲートウェイ) ではトラフィックを転送できないからです。すなわち、2 つの回線を同じゲートウェイに接続できますが、一方の回線から他方にトラフィックが送信されません。

>[!NOTE]
>たとえば地域の規制のために、ExpressRoute Global Reach が有効になっていない場所では、Azure IaaS VM を使用してルーティング ソリューションを構築する必要があります。 いくつかの例については、[AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking) を参照してください。

