---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: c80e256691066239d89b101f206f758725809fad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909629"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 これは、回線を Microsoft Enterprise Edge (MSEE) レベルで直接接続します。 この接続には、サブスクリプション内にオンプレミスへの ExpressRoute 回線がある仮想ネットワーク (vNet) が必要です。  その理由は、vNet ゲートウェイ (ExpressRoute ゲートウェイ) ではトラフィックを転送できないからです。すなわち、2 つの回線を同じゲートウェイに接続できますが、一方の回線から他方にトラフィックが送信されません。

各 Azure VMware Solution 環境は独自の ExpressRoute リージョン (独自の仮想 MSEE デバイス) であり、この環境では Global Reach を "ローカル" ピアリングの場所に接続できます。  これにより、1つのリージョンにある複数の Azure VMware Solution インスタンスを同じピアリングの場所に接続できます。 

>[!NOTE]
>たとえば地域の規制のために、ExpressRoute Global Reach が有効になっていない場所では、Azure IaaS VM を使用してルーティング ソリューションを構築する必要があります。 いくつかの例については、[AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking) を参照してください。

