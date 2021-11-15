---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2064586fafd82cfc67e14b289797a562e3512afd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245154"
---
* 専用コンピューティング価格レベル。Basic、Standard、Premium、PremiumV2、PremiumV3 が含まれています。
* 専用のサポート インフラストラクチャを使用して VNet に直接デプロイされ、Isolated と IsolatedV2 の価格レベルを使用している App Service Environment。

VNet 統合機能は、App Service 専用のコンピューティング価格レベルで使用されます。 アプリが [App Service Environment](../articles/app-service/environment/overview.md) 内にある場合、そのアプリは既に VNet 内に存在するため、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。 すべてのネットワーク機能の詳細については、「[App Service のネットワーク機能](../articles/app-service/networking-features.md)」を参照してください。

VNet 統合により、アプリから VNet 内のリソースにアクセスできるようになりますが、VNet からそのアプリへの受信プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワーク内など、プライベート ネットワークのみからアプリにアクセスできるようにすることです。 VNet 統合は、アプリから VNet への送信呼び出しを行うためだけに使用されます。 VNet 統合機能は、同じリージョン内の VNet で使用する場合と、他のリージョン内の VNet で使用する場合とで、動作が異なります。 VNet 統合機能には 2 つのバリエーションがあります。

* **リージョン Vnet 統合**: 同じリージョン内の仮想ネットワークに接続するときは、統合する VNet に専用のサブネットが必要です。
* **ゲートウェイが必要な VNet 統合**: 他のリージョン内の VNet または同じリージョン内のクラシック仮想ネットワークに直接接続する場合、ターゲットの VNet に作成された Azure 仮想ネットワーク ゲートウェイが必要です。

以下は、VNet 統合機能の特徴です。

* **Standard**、**Premium**、**PremiumV2**、**PremiumV3**、または **Elastic Premium** の App Service 価格レベルが必要です。
* TCP と UDP をサポートします。
* Azure App Service アプリや関数アプリで動作します。

以下のような一部の機能は、VNet 統合ではサポートされません。

* ドライブのマウント。
* Windows Server の Active Directory の統合。
* NetBIOS。

ゲートウェイが必要な VNet 統合では、ターゲット VNet 内、あるいはピアリングまたは VPN を使用してターゲット VNet に接続されているネットワーク内のリソースのみにアクセスできます。 ゲートウェイが必要な VNet 統合では、Azure ExpressRoute 接続全体で利用可能なリソースへのアクセスを有効にしたり、サービス エンドポイントと連携したりすることはできません。

使用されているバージョンに関係なく、VNet 統合によってアプリに VNet 内のリソースへのアクセスが付与されますが、VNet からアプリへの受信プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワーク内など、プライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。
