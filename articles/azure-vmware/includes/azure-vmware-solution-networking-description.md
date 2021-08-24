---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d758d42ff1e5c77ddd1e17811e2eea24717878aa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070743"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースのリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute、VPN 接続、Azure Virtual WAN などのサービスによって接続が可能になります。 しかし、これらのサービスでは、サービスを有効にするために特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

プライベート クラウドにデプロイされた仮想マシンは、[Azure Virtual WAN パブリック IP](../enable-public-internet-access.md) 機能を使用してインターネットからアクセスできます。 新しいプライベート クラウドでは、インターネット アクセスが既定で無効になっています。 



