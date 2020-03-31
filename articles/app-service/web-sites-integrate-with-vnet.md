---
title: Azure Virtual Network とのアプリの統合
description: Azure App Service のアプリを Azure Virtual Network と統合します。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673220"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>アプリを Azure 仮想ネットワークに統合する
このドキュメントでは、Azure App Service の仮想ネットワーク統合機能と、それを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) のアプリで設定する方法について説明します。 [Azure Virtual Network][VNETOverview] (VNet) を使用すると、多くの Azure リソースをインターネットでルーティングできないネットワークに配置できます。  

Azure App Service には、次の 2 つのバリエーションがあります。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNET 統合を有効にする 

1. App Service ポータルで [ネットワーク] の UI に移動します。 [VNet 統合] の下の [*ここをクリックして構成*] を選択します。 

1. **[Add VNet]\(VNet の追加)** を選択します。  

   ![Vnet 統合を選択する][1]

1. ドロップダウン リストには、同じリージョンのサブスクリプション内のすべての Resource Manager VNet が含まれており、その下は、他のすべてのリージョンのすべての Resource Manager VNet の一覧です。 統合する VNet を選択します。

   ![VNet の選択][2]

   * VNet が同じリージョンにある場合は、新しいサブネットを作成するか、空の既存のサブネットを選択します。 

   * 別のリージョンの VNet を選択するには、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

   * クラシック VNet と統合するには、VNet ドロップダウンをクリックするのではなく、 **[クラシック VNet に接続するには、ここをクリックします]** を選択します。 目的のクラシック VNet を選択します。 ターゲットの VNet には、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

    ![クラシック VNet の選択][3]
    
統合中にアプリは再起動されます。  統合が完了すると、統合されている VNet の詳細が表示されます。 

VNet に統合されたアプリは、VNet が構成されているのと同じ DNS サーバーを使用します (Azure DNS Private Zones の場合を除く)。 現時点では、Azure DNS Private Zones で VNet 統合を使用することはできません。

## <a name="regional-vnet-integration"></a>リージョン VNet 統合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>リージョン VNET 統合のしくみ

App Service 内のアプリは、worker ロールでホストされます。 Basic 以上の価格プランは、同じワーカー上で他の顧客のワークロードが実行されていない専用のホスティング プランです。 リージョン VNet 統合は、委任されたサブネット内のアドレスで仮想インターフェイスをマウントすることによって機能します。 送信元アドレスは VNet 内に存在するため、VNet 内の VM と同様に、VNet 内または VNet 経由でほとんどの場所にアクセスできます。 ネットワークの実装は VNet 内の VM の実行とは異なるため、この機能の使用中に、一部のネットワーク機能はまだ使用できません。

![リージョン VNET 統合のしくみ][5]

リージョン VNet 統合が有効になっても、アプリは引き続き、通常と同じチャネル経由でインターネットへの送信呼び出しを行います。 アプリのプロパティ ポータルに一覧表示される送信アドレスは引き続き、そのアプリによって使用されるアドレスです。 アプリに関する変更は、サービス エンドポイントでセキュリティ保護されたサービスまたは RFC 1918 アドレスへの呼び出しが VNet に転送されることです。 WEBSITE_VNET_ROUTE_ALL が 1 に設定されている場合、すべての送信トラフィックを VNet 内に送信できます。 

この機能は、ワーカーあたり 1 つの仮想インターフェイスのみをサポートします。  worker あたり 1 つの仮想インターフェイスは、App Service プランごとに 1 リージョンの VNet 統合があることを意味します。 同じ App Service プラン内のすべてのアプリが同じ Vnet 統合を使用できますが、アプリで追加の VNet に接続する必要がある場合は、別の App Service プランを作成する必要があります。 使用される仮想インターフェイスは、顧客が直接アクセスできるリソースではありません。

このテクノロジが動作する方法の性質のために、Vnet 統合で使用されるトラフィックは Network Watcher や NSG フロー ログには表示されません。  

## <a name="gateway-required-vnet-integration"></a>ゲートウェイが必要な VNet 統合

ゲートウェイが必要な VNet 統合では、別のリージョンの VNet またはクラシック VNet への接続をサポートしています。 ゲートウェイが必要な VNet 統合: 

* アプリは、一度に 1 つの VNet にのみ接続できます
* App Service プランで、最大 5 つの VNet と統合できます 
* App Service プランで使用できる総数に影響を与えることなく、App Service プラン内の複数のアプリで同じ VNet を使用できます。  同じ App Service プラン内の同じ VNet を使用しているアプリが 6 つある場合、使用されている VNet は 1 つとしてカウントされます。 
* ゲートウェイに対する SLA により、99.9% の SLA をサポートします
* VNet が構成されている DNS をアプリで使用できるようにします
* アプリに接続するために、Virtual Network のルート ベースのゲートウェイが SSTP ポイント対サイト VPN を使用して構成されている必要があります。 

ゲートウェイが必要な VNet 統合は次の場合は使用できません。

* Linux アプリに対して
* ExpressRoute に接続されている VNet に対して 
* サービス エンドポイントによって保護されているリソースにアクセスする場合
* ExpressRoute とポイント対サイト/サイト間 VPN の両方をサポートする共存ゲートウェイに対して

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet 内にゲートウェイを設定する ###

ゲートウェイを作成するには:

1. VNet 内に[ゲートウェイ サブネットを作成][creategatewaysubnet]します。  

1. [VPN ゲートウェイを作成][creategateway]します。 VPN の種類はルート ベースを選択します。

1. [ポイント対サイト アドレスを設定][setp2saddresses]します。 ゲートウェイが Basic SKU 内にない場合は、ポイント対サイトの構成で IKEV2 を無効にする必要があり、SSTP を選択する必要があります。 ポイント対サイトのアドレス空間は、RFC 1918 アドレス ブロック 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 内に存在する必要があります。

App Service の VNet 統合で使用するゲートウェイを作成しているだけの場合は、証明書をアップロードする必要はありません。 ゲートウェイの作成には 30 分かかることがあります。 ゲートウェイがプロビジョニングされるまで、アプリを VNet に統合することはできません。 

### <a name="how-gateway-required-vnet-integration-works"></a>ゲートウェイが必要な VNet 統合のしくみ

ゲートウェイが必要な Vnet 統合は、ポイント対サイト VPN テクノロジに基づいて構築されています。 ポイント対サイト VPN によって、ネットワーク アクセスを、アプリをホストしている仮想マシンのみに制限しています。 アプリは、ハイブリッド接続または VNet 統合を通してのみ、インターネットにトラフィックを送信するように制限されています。 ゲートウェイが必要な VNet 統合を使用するようにアプリをポータルで構成すると、ゲートウェイとアプリケーション側で証明書を作成して割り当てるための複雑なネゴシエーションがユーザーの代わりに管理されます。 最終的に、アプリをホストするために使用される worker は、選択した VNet 内の仮想ネットワーク ゲートウェイに直接接続できるようになります。 

![ゲートウェイが必要な VNet 統合のしくみ][6]

### <a name="accessing-on-premises-resources"></a>オンプレミスのリソースへのアクセス

アプリは、サイト間接続がある Vnet と統合することで、オンプレミスのリソースにアクセスできます。 ゲートウェイが必要な Vnet 統合を使用している場合は、ポイント対サイト アドレス ブロックでオンプレミスの VPN ゲートウェイのルートを更新する必要があります。 サイト間 VPN が初めてセットアップされるときには、その構成に使用するスクリプトによって、ルートが正しくセットアップされるはずです。 サイト間 VPN を作成した後にポイント対サイト アドレスを追加する場合は、ルートを手動で更新する必要があります。 その詳しい手順はゲートウェイごとに異なるため、ここでは取り上げません。 BGP をサイト間 VPN 接続で構成することはできません。

VNet 経由でオンプレミスに到達するために、リージョン Vnet 統合機能に追加の構成は必要ありません。 必要なのは、単に ExpressRoute またはサイト間 VPN を使用して VNet をオンプレミスに接続することだけです。 

> [!NOTE]
> ゲートウェイが必要な Vnet 統合機能では、アプリは ExpressRoute ゲートウェイを含む VNet とは統合されません。 ExpressRoute ゲートウェイが[共存モード][VPNERCoex]で構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続経由でリソースにアクセスする必要がある場合は、VNet で実行されるリージョン VNet 統合機能または [App Service Environment][ASE] を使用できます。 
> 
> 

### <a name="peering"></a>ピアリング

リージョン Vnet 統合でピアリングを使用している場合、追加の構成を行う必要はありません。 

ゲートウェイが必要な Vnet 統合でピアリングを使用している場合は、いくつかの追加の項目を構成する必要があります。 アプリで動作するようにピアリングを構成するには:

1. アプリの接続先の VNet でピアリング接続を追加します。 ピアリング接続の追加時には、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[ゲートウェイ転送を許可する]** チェック ボックスをオンにします。
1. 接続先の Vnet にピアリングされている VNet でピアリング接続を追加します。 対象の VNet でピアリング接続を追加するときには、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[Allow remote gateways]\(リモート ゲートウェイを許可する)** チェック ボックスをオンにします。
1. ポータルで [App Service プラン] > [ネットワーク] > VNet 統合 UI と移動します。  アプリの接続先 VNet を選択します。 ルーティングのセクションで、アプリの接続先 VNet とピアリングされている VNet のアドレス範囲を追加します。  

## <a name="managing-vnet-integration"></a>Vnet 統合の管理 

VNet との接続および切断は、アプリ レベルで行われます。 複数のアプリをまたいで VNet 統合に影響する可能性がある操作は、App Service プラン レベルで行われます。 アプリ > [ネットワーク] > [Vnet 統合] ポータルから、VNet に関する詳細を取得できます。 [ASP] > [ネットワーク] > [VNet 統合] ポータルの ASP レベルでも同様の情報が表示されます。

VNet 統合のアプリ ビューで実行できる操作は、現在接続されている VNet からアプリを切断する操作のみです。 VNet からアプリを切断するには、 **[切断]** を選択します。 VNet から切断すると、アプリは再起動されます。 切断によって VNet に変更は生じません。 サブネットやゲートウェイは削除されません。 その後で VNet を削除する場合は、まず VNet からアプリを切断し、ゲートウェイなどのそこに含まれているリソースを削除する必要があります。 

ASP VNet 統合 UI には、ASP でアプリが使用しているすべての VNet 統合が表示されます。 各 VNet の詳細を表示するには、目的の VNet をクリックします。 ここでは、ゲートウェイが必要な VNet 統合に対して実行できるアクションが 2 つあります。

* **ネットワークを同期する**。 ネットワーク同期操作は、ゲートウェイに依存する VNet 統合機能のためにのみ存在します。 ネットワーク同期操作を実行すると、証明書とネットワークの情報が確実に同期されるようになります。VNet の DNS を変更する場合は、**ネットワークの同期**操作を実行する必要があります。 この操作によって、この VNet を使用しているすべてのアプリが再起動されます。
* **ルートを追加する** ルートを追加すると、送信トラフィックが VNet に誘導されます。 

**ゲートウェイが必要な VNet 統合のルーティング** VNet に定義されたルートは、アプリから VNet にトラフィックを転送するために使用されます。 VNet に追加の送信トラフィックを送信する必要がある場合は、ここでそれらのアドレス ブロックを追加できます。 この機能は、ゲートウェイが必要な Vnet 統合でのみ動作します。 ルート テーブルは、ゲートウェイが必要な VNet 統合を使用する場合、アプリのトラフィックに影響しません。これはリージョン VNet 統合の場合とは異なります。

**ゲートウェイが必要な VNet 統合の証明書** ゲートウェイが必要な Vnet 統合が有効になっている場合は、接続のセキュリティを確保するために証明書の交換が必要です。 証明書と共に、DNS の構成、ルート、ネットワークについて記述するその他の同様の情報があります。
証明書またはネットワーク情報が変更された場合は、[ネットワークの同期] をクリックする必要があります。 [ネットワークの同期] をクリックすると、アプリと VNet の間の接続が短時間途切れます。 アプリが再起動されていない間は、接続の途切れによって、サイトが正常に機能しなくなる可能性があります。 

## <a name="pricing-details"></a>価格の詳細
リージョン Vnet 統合機能には、ASP の価格レベルの料金を超える追加の使用料金はありません。

ゲートウェイが必要な Vnet 統合機能の使用に関連する料金には、次の 3 つがあります。

* ASP の価格レベルの料金 - アプリは Standard、Premium、または PremiumV2 の App Service プランに属している必要があります。 これらのコストの詳細については、「[App Service の価格][ASPricing]」を参照してください。 
* データ転送のコスト - VNet が同じデータ センター内に存在する場合でも、データ エグレスの料金が発生します。 これらの料金は、「[帯域幅の料金詳細][DataPricing]」で説明されています。 
* VPN Gateway のコスト - ポイント対サイト VPN に必要な VNet ゲートウェイに対してコストが発生します。 これらの詳細は、「[VPN Gateway の価格][VNETPricing]」のページに記載されています。

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

リージョン VNet 統合では CLI がサポートされています。 次のコマンドにアクセスするには、[Azure CLI をインストール][installCLI]します。 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

ゲートウェイが必要な VNet 統合では、PowerShell を使用して App Service を Azure Virtual Network と統合できます。 すぐに使用できるスクリプトについては、「[Connect an app in Azure App Service to an Azure Virtual Network (Azure App Service のアプリを Azure 仮想ネットワークに接続する)](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)」をご覧ください。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
