---
title: Azure ExpressRoute Global Reach のアプリケーション シナリオ | Microsoft Docs
description: このページでは、Global Reach のアプリケーション シナリオを示します。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333088"
---
# <a name="expressroute-global-reach-application-scenario"></a>ExpressRoute Global Reach のアプリケーション シナリオ

ExpressRoute Global Reach の詳細については、「[ExpressRoute Global Reach][Global Reach]」をご覧ください。 この記事では、アプリケーション シナリオについて説明し、ExpressRoute Global Reach ソリューションを他のいくつかのソリューションと比較し、シナリオ例に合わせて Global Reach を構成して、接続を確認します。 

##<a name="application-scenario"></a>アプリケーション シナリオ

Fabrikam, Inc. は、米国東部に大規模な拠点があり、Azure をデプロイしています。 Fabrikam には、オンプレミスのデプロイと ExpressRoute 経由の Azure デプロイの間にバックエンド接続があります。 Contoso Ltd. は、米国西部に拠点があり、Azure をデプロイしています。 Contoso には、オンプレミスのデプロイと ExpressRoute 経由の Azure デプロイの間にバックエンド接続があります。  

Fabrikam Inc. は Contoso Ltd. を買収します。この合併の後、Fabrikam はネットワークを相互接続します。 このシナリオを次の図に示します。

 [![1]][1]

上の図の真ん中の破線の矢印は、必要なネットワーク相互接続を示しています。 次の表は、合併前の Contoso Ltd. の ExpressRoute のプライマリのプライベート ピアリングのルート テーブルを示しています。

[![2]][2]

次の表は、合併前の Fabrikam Inc. の ExpressRoute のプライマリのプライベート ピアリングのルート テーブルを示しています。

[![3]][3]

## <a name="traditional-solutions"></a>従来のソリューション

### <a name="option-1-interconnect-on-premises-networks"></a>オプション 1:オンプレミスの相互接続ネットワーク

このオプションを次の図に示します。 示されているように、サイト間 VPN またはその他のブロードバンド接続のオプションを使用して 2 つのオンプレミス ネットワークを相互接続して、2 つの組織の間ですべてのルーティングを管理することができます。 

[![4]][4]

このオプションには次のような欠点があります。

- 最適でないルート経由でのデプロイのためにリージョン間の Azure 通信を強制することになります。
- リージョン間の通信のための Microsoft バックボーン ネットワークの高可用性のメリットを拒否することになります。
- リージョン間の通信のルーティングに対する完全な責任を負うことになります。

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>オプション 2:ExpressRoute のクロス接続とオンプレミスの相互接続ネットワーク

このオプションを次の図に示します。

[![5]][5]

上の図に示されているように、さらに、ExpressRoute 回線とリージョンをまたがる VNet の間の接続を確立できます。 VNet と ExpressRoute 回線の間のクロス リージョン接続により、次の通信が有効になります。

- 米国西部と米国東部の VNet。それぞれが Fabrikam と Contoso のオンプレミス ネットワークと通信します。
- 米国東部の VNet と通信する米国西部の VNet。

2 つのオンプレミス ネットワーク間の相互接続は、互いに通信するために引き続きオンプレミス ネットワークに必要です。

このオプションを使用すると、プライベートのデプロイのためのリージョン間の Azure 通信を Microsoft バックボーン経由で処理でき、オンプレミス ネットワーク間の通信を外部ネットワーク上で実行できます。 ただし、このソリューションの主な欠点は、複数のクロス リージョン接続を確立する必要があり、それによってメンテナンスとトラブルシューティングが複雑になることです。 また、このオプションでは、2 つのオンプレミス ネットワーク間の通信に対して可用性の高い Microsoft のグローバル バックボーンを活用できません。

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>オプション 3: VNet ピアリングとオンプレミスの相互接続ネットワーク

このオプションを次の図に示します。 このオプションは、リージョン間 VNet 通信に VNet ピアリングを使用しています。 図に示されているように、このオプションは、リージョン間 VNet とオンプレミスとの通信に対応していないため、不完全です (真ん中の 2 本の点線の矢印で示されています)。 リージョン間とオンプレミスの通信のためには、(オプション 1 のように) オンプレミス間の接続を使用するか、(オプション 2 のように) ExpressRoute のクロス接続を使用してください。

[![6]][6]

このオプションは、リージョン間 VNet 通信に最適なルーティングを提供します。 ただし、Fabrikam と Contoso のいずれかにハブ アンド スポーク VNet モデルなどのより複雑な Azure デプロイがある場合は不十分です。 また、前の 2 つのオプションと同様に、このオプションでは、2 つのオンプレミス ネットワーク間の通信に対して可用性の高い Microsoft のグローバル バックボーンを活用できません。

## <a name="global-reach"></a>Global Reach

次の図に示すように、ExpressRoute Global Reach は ExpressRoute 回線のプライベート ピアリングをリンクします。

[![7]][7]

Global Reach を 2 つの ExpressRoute 回線の間に構成すると、2 つのオンプレミス ネットワークと 2 つのリージョンの Azure デプロイの間のプライベート通信が可能になります。 したがって、Global Reach は、Microsoft バックボーン ネットワーク経由のすべての必要な通信 (この記事の最初の図の破線で示されています) に対応します。

### <a name="configure-global-reach"></a>Global Reach を構成する

ExpressRoute Global Reach の詳細については、[Global Reach の構成][Configure Global Reach]に関するページをご覧ください。 

Fabrikam, Inc. と Contoso ltd. は別々の会社として Azure をオンボードしたため、2 つの会社の ExpressRoute 回線は 2 つの異なる Azure サブスクリプションにあります。 サブスクリプションをまたがる Global Reach を作成するには、Contoso ltd. に属している ExpressRoute 回線の承認を作成して Fabrikam, Inc. に渡す必要があります。ExpressRoute 回線。


Contoso の ExpressRoute 回線の承認を作成するには、最初に Contoso の Azure アカウントにログインして、(複数のサブスクリプションがある場合は) 適切なサブスクリプションを選択します。 これらの手順の PowerShell コマンドは、以下のとおりです。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
ExpressRoute 回線の承認を作成する手順を、以下に示します。

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

'Set-AzureRmExpressRouteCircuit' の出力に ExpressRouteCircuit が一覧表示されます。 一覧の最後の方に表示されるプライベート ピアリング ID と承認キーをメモしておきます。 次の PowerShell 出力スニペットの例を参照してください。

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

ピアリングの ID と認証キーを使用して、Fabrikam の ExpressRoute 回線に Global Reach を作成することができます。 Fabrikam の Azure アカウントにログインします。 複数のサブスクリプションがある場合は、適切なサブスクリプションを選択します。

Global Reach は、2 つの MSEE ペアの間にポイント ツー ポイント接続の冗長なセットを作成します。 2 つのポイント ツー ポイント接続の場合は、/29 アドレス プレフィックスを指定する必要があります (実行中の例では、192.168.11.64/29 を使用してみましょう)。 Global Reach 接続を作成するには、次のコマンドを実行します。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

上記のコマンドを実行した後、冗長な Global Reach 接続を作成するのに数分かかります。

### <a name="verify-global-reach"></a>Global Reach を確認する

次の表は、Global Reach の構成後の Contoso Ltd. の ExpressRoute のプライマリのプライベート ピアリングのルート テーブルを示しています。

[![8]][8]

次の表は、Global Reach の構成後の Fabrikam Inc. の ExpressRoute のプライマリのプライベート ピアリングのルート テーブルを示しています。

[![9]][9]

上の表では、期待された宛先 'NETWORK' のすべてのプレフィックスと適切な 'NEXT HOP' が一覧表示されていることがわかります。

上部には、ExpressRoute 回線の 'プライベート ピアリング' の下に、Azure portal でアクセスできる 'ルート テーブルの取得' ブレードが表示されています。 また、次の PowerShell コマンドを使用して ExpressRoute ルート テーブルを一覧表示することもできます。

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

セカンダリ MSEE のルート テーブルを表示するには、上記のコマンドの "primary" をキーワード 'secondary' に置き換えます。

また、さまざまなネットワークから さまざまな宛先への ping を実行することで、データ プレーン接続も確認しましょう。 次の 3 つの ping は、Fabrikam のオンプレミス ネットワークから Contoso のオンプレミス ネットワーク、Contoso の Azure VNet、および Fabrikam の Azure VNet へのデータ プレーン接続を確認します。


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


次の 2 つの ping は、Contoso のオンプレミス ネットワークから Contoso の Azure VNet および Fabrikam の Azure VNet へのデータ プレーン接続を確認します。

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

次の ping は、Fabrikam の Azure VNet から Contoso の Azure VNet へのデータ プレーン接続を確認します。

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>待機時間の影響を受けやすいトラフィックの最適化

Global Reach は Microsoft Edge デバイスを使用してトラフィックをルーティングします。 この記事で検討した特定のシナリオでは、2 つの VNet の間の VNet ピアリングを有効にすることで、それらの間により最適なルーティングを実現できます。 同様に、サイト間のより直接的な WAN 接続を提供できるサービス プロバイダーを使用して、2 つのオンプレミス ネットワーク間により最適なルーティングを実現できます。 このようなシナリオでは、Global Reach のルーティングをこれらの接続のフェールバックのオプションとして使用することができます。 

## <a name="next-steps"></a>次の手順

Global Reach は国ごとにロールアウトされています。 Global Reach が必要な国で使用可能かどうかを確認するには、「[ExpressRoute Global Reach][Global Reach]」を参照してください。

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "アプリケーションのシナリオ"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "合併前の Contoso の ExpressRoute ルート テーブル"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "合併前の Fabrikam の ExpressRoute ルート テーブル"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "オンプレミス ネットワークを相互接続する"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Express Route 交差接続"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet ピアリング"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Global Reach を使用した Contoso の ExpressRoute ルート テーブル"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Global Reach を使用した Fabrikam の ExpressRoute ルート テーブル"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





