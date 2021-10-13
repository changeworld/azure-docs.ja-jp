---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: add146bb5b64e12b09bec89c080e1222154d4e2f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638279"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 これは、回線を Microsoft Enterprise Edge (MSEE) レベルで直接接続します。 この接続には、サブスクリプション内にオンプレミスへの ExpressRoute 回線がある仮想ネットワーク (vNet) が必要です。  その理由は、vNet ゲートウェイ (ExpressRoute ゲートウェイ) ではトラフィックを転送できないからです。すなわち、2 つの回線を同じゲートウェイに接続できますが、一方の回線から他方にトラフィックが送信されません。

各 Azure VMware Solution 環境は独自の ExpressRoute リージョン (独自の仮想 MSEE デバイス) であり、この環境では Global Reach を "ローカル" ピアリングの場所に接続できます。  これにより、1つのリージョンにある複数の Azure VMware Solution インスタンスを同じピアリングの場所に接続できます。 

>[!NOTE]
>たとえば地域の規制のために、ExpressRoute Global Reach が有効になっていない場所では、Azure IaaS VM を使用してルーティング ソリューションを構築する必要があります。 いくつかの例については、[AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking) を参照してください。

