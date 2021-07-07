---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 05/28/2021
ms.openlocfilehash: 66ea12565aa84661a4019e5685a5544792591b0c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350780"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースのリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute、VPN 接続、Azure Virtual WAN などのサービスによって接続が可能になります。 これらのサービスでは、サービスを有効にするための特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。  

ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 この接続には、サブスクリプション内にオンプレミスへの ExpressRoute 回線がある仮想ネットワークが必要です。

プライベート クラウドにデプロイされた仮想マシン (VM) は、Azure Virtual WAN パブリック IP 機能を使用してインターネットからアクセスできます。  新しいプライベート クラウドのインターネット アクセスは、既定で無効になっています。 詳細については、[Azure VMware Solution でパブリック インターネット アクセスを有効にする](../enable-public-internet-access.md)方法に関する記事を参照してください。



