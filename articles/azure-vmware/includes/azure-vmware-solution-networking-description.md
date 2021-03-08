---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925028"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースの環境またはリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute 接続や VPN 接続などのサービスによって接続が可能になります。 これらのサービスでは、サービスを有効にするための特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。  ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 この接続には、サブスクリプション内に ExpressRoute 回線がある仮想ネットワークが必要です。

Web サーバーや仮想マシンなどのリソースからは、Azure Virtual WAN パブリック IP 機能を使用してインターネットにアクセスできます。  新しいプライベート クラウドのインターネット アクセスは、既定で無効になっています。 詳細については、「[Azure VMware Solution でパブリック IP 機能を使用する方法](../public-ip-usage.md)」を参照してください。