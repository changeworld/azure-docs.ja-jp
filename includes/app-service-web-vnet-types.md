---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312835"
---
* Isolated を除くすべての価格プランをサポートするマルチテナント システム
* VNet にデプロイされ、Isolated 価格プランのアプリをサポートする App Service Environment。

VNet 統合機能はマルチテナント アプリで使用されます。 アプリが [App Service Environment][ASEintro] 内にある場合、そのアプリは既に VNet 内に存在するため、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。 すべてのネットワーク機能の詳細については、「[App Service のネットワーク機能][Networkingfeatures]」を参照してください。

VNet 統合により、アプリから VNet 内のリソースにアクセスできるようになりますが、VNet からそのアプリへの受信プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワーク内など、プライベート ネットワークのみからアプリにアクセスできるようにすることです。 VNet 統合は、アプリから VNet への送信呼び出しを行うためだけに使用されます。 VNet 統合機能は、同じリージョン内の VNet で使用する場合と、他のリージョン内の VNet で使用する場合とで、動作が異なります。 VNet 統合機能には 2 つのバリエーションがあります。

* **リージョン VNet 統合**:同じリージョン内の Azure Resource Manager 仮想ネットワークに接続する場合、統合する VNet での専用のサブネットが必要になります。
* **ゲートウェイが必要な VNet 統合**:他のリージョン内の VNet または同じリージョン内のクラシック仮想ネットワークに接続する場合、ターゲットの VNet にプロビジョニングされた Azure 仮想ネットワーク ゲートウェイが必要です。

以下は、VNet 統合機能の特徴です。

* Standard、Premium、PremiumV2、または Elastic Premium の価格プランが必要である。
* TCP と UDP をサポートする。
* Azure App Service アプリや関数アプリで動作する。

以下のような一部の機能は、VNet 統合ではサポートされません。

* ドライブのマウント。
* Active Directory 統合。
* NetBIOS。

ゲートウェイが必要な VNet 統合では、ターゲット VNet 内、あるいはピアリングまたは VPN を使用してターゲット VNet に接続されているネットワーク内のリソースのみにアクセスできます。 ゲートウェイが必要な VNet 統合では、Azure ExpressRoute 接続全体で利用可能なリソースへのアクセスを有効にしたり、サービス エンドポイントと連携したりすることはできません。

使用されているバージョンに関係なく、VNet 統合によってアプリに VNet 内のリソースへのアクセスが付与されますが、VNet からアプリへの受信プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure VNet 内など、プライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
