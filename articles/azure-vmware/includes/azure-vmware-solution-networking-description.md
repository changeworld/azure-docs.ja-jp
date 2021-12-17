---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638551"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースのリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute、VPN 接続、Azure Virtual WAN などのサービスによって接続が可能になります。 しかし、これらのサービスでは、サービスを有効にするために特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

プライベート クラウドにデプロイされた仮想マシンは、[Azure Virtual WAN パブリック IP](../enable-public-internet-access.md) 機能を使用してインターネットからアクセスできます。 新しいプライベート クラウドでは、インターネット アクセスが既定で無効になっています。 



