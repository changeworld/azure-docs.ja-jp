---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671480"
---
* Isolated を除くすべての価格プランをサポートするマルチテナント システム
* VNet にデプロイされ、Isolated 価格プランのアプリをサポートする App Service Environment (ASE)

このドキュメントでは、マルチテナント アプリでの使用に向けた VNet 統合機能について説明します。 アプリが [App Service Environment][ASEintro] 内にある場合、そのアプリは既に VNet 内に存在するため、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。 すべてのネットワーク機能の詳細については、「[App Service のネットワーク機能][Networkingfeatures]」を参照してください。

VNet 統合により、アプリから仮想ネットワーク内のリソースにアクセスできるようになりますが、VNet からそのアプリへの受信プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。 VNet 統合機能は、同じリージョンの VNet と使用する場合と、他のリージョンの VNet で使用する場合で動作が異なります。 VNet 統合機能には 2 つのバリエーションがあります。

* リージョン VNet 統合 - 同じリージョンで Resource Manager VNet に接続する場合は、統合先の VNet に専用のサブネットが必要です。 
* ゲートウェイが必要な VNet 統合 - 他のリージョンの VNet または同じリージョンのクラシック VNet に接続する場合、ターゲット VNet にプロビジョニングされた Virtual Network ゲートウェイが必要です。

以下は、VNet 統合機能の特徴です。

* Standard、Premium、PremiumV2、または Elastic Premium の価格プランが必要 
* TCP と UDP をサポート
* App Service アプリや関数アプリで動作可能

以下に、VNet 統合でサポートされていないことの例を示します。

* ドライブのマウント
* AD 統合 
* NetBIOS

ゲートウェイが必要な VNet 統合では、ターゲット VNet 内、またはピアリングまたは VPN を使用してターゲット VNet に接続されているネットワーク内のリソースのみにアクセスできます。 ゲートウェイが必要な VNet 統合では、ExpressRoute 接続全体で利用可能なリソースへのアクセスを有効にしたり、サービス エンドポイントと連携したりできません。 

使用されているバージョンには関係なく、VNet 統合によりアプリに仮想ネットワーク内のリソースへのアクセス権が付与されますが、仮想ネットワークからアプリへの受信プライベート アクセス権は付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features