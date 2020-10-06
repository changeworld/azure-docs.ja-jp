---
title: Azure VMware Solution のネットワークと接続
description: Azure VMWare Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574460"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースの環境またはリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute 接続や VPN 接続などのサービスによって接続が可能になります。 これらのサービスでは、サービスを有効にするための特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。  ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 この接続には、サブスクリプション内に ExpressRoute 回線がある仮想ネットワークが必要です。



>[!NOTE]
>プライベート クラウドをデプロイするときに、運用ネットワーク上の VM を使用するために、インターネットと Azure サービスへのアクセスがプロビジョニングされ、提供されます。  既定では、新しいプライベート クラウドのインターネット アクセスは無効になっており、いつでも有効または無効にすることができます。