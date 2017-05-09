---
title: "Azure Application Gateway を作成および管理する - PowerShell | Microsoft Docs"
description: "このページでは、Azure リソース マネージャーを使用して、Azure Application Gateway を作成、構成、起動、および削除する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Azure リソース マネージャーを使用した、Application Gateway の作成、起動、または削除

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。
Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。

サポートされている機能の完全な一覧については、「 [Application Gateway の概要](application-gateway-introduction.md)

この記事では、Application Gateway を作成、構成、起動、および削除する手順について説明します。

> [!IMPORTANT]
> Azure リソースを使用する前に、Azure には現在 Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの 2 種類があることを理解しておくことが重要です。 Azure リソースを使用する前に、必ず [デプロイメント モデルとツール](../azure-classic-rm.md) について理解しておいてください。 この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。 このドキュメントでは、Azure Resource Manager を使用したアプリケーション ゲートウェイの作成について説明します。 クラシック バージョンを使用する場合は、 [PowerShell を使用したアプリケーション ゲートウェイ クラシック デプロイメントの作成](application-gateway-create-gateway.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
1. 既存の仮想ネットワークがある場合は、既存の空のサブネットを選択するか、既存の仮想ネットワークにアプリケーション ゲートウェイ専用のサブネットを作成します。 アプリケーション ゲートウェイの背後にデプロイするリソースとは異なる仮想ネットワークにアプリケーション ゲートウェイをデプロイすることはできません。
1. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。つまり、仮想ネットワーク内にエンドポイントが作成されているか、割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## <a name="what-is-required-to-create-an-application-gateway"></a>Application Gateway の作成に必要な構成

* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレス、FQDN、または NIC の一覧。 使用する IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Azure クラシックと Azure Resource Manager の使用方法の違いは、設定が必要なアプリケーション ゲートウェイと項目を作成する順番にあります。

Resource Manager を使用すると、アプリケーション ゲートウェイを作成するすべての項目は個別に構成され、その後結合されてアプリケーション ゲートウェイのリソースが作成されます。

アプリケーション ゲートウェイを作成するために必要な手順を次に示します。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure へのログイン

```powershell
Login-AzureRmAccount
```

資格情報を使用して認証を行うように求めるメッセージが表示されます。

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、**appgw-RG** という名前のリソース グループと **West US** という名前の場所を作成しました。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、[PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、 [カスタム プローブと正常性監視](application-gateway-probe-overview.md) に関するページを参照してください。

## <a name="create-a-virtual-network-and-a-subnet"></a>仮想ネットワークとサブネットの作成

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。 この例では、アプリケーション ゲートウェイ用の VNET を作成します。 アプリケーション ゲートウェイには独自のサブネットが必要です。そのため、アプリケーション ゲートウェイ用に作成するサブネットは、VNET のアドレス空間よりも小さくします。 サブネットを小さくすることで、VNET を他のリソースのために活用できます。一例としては、同じ VNET 内で Web サーバーを構成できます。

### <a name="step-1"></a>手順 1

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。 この手順ではアプリケーション ゲートウェイのサブネット構成オブジェクトを作成します。これは次のサンプル コードで使用します。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>手順 2.

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **appgw-rg** に、**appgwvnet** という名前の仮想ネットワークを作成します。 この手順では、アプリケーション ゲートウェイを配置する 1 つのサブネットが含まれた VNET の構成を完了します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>手順 3.

次の手順のためにサブネット変数を割り当てます。この変数は、後の手順で `New-AzureRMApplicationGateway` コマンドレットに渡されます。

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

米国西部リージョンのリソース グループ **appgw-rg** に、パブリック IP リソース **publicIP01** を作成します。 Application Gateway では、パブリック IP アドレス、内部 IP アドレス、またはその両方を、負荷分散の要求の受信に使用できます。  この例で使用するのはパブリック IP アドレスだけです。 次の例でのパブリック IP アドレスの作成では、DNS 名の構成を行いません。  Application Gateway では、パブリック IP アドレスのカスタム DNS 名はサポートされていません。  パブリック エンドポイントにカスタム名が必要な場合は、CNAME レコードを作成し、パブリック IP アドレス用に自動生成される DNS 名を指定する必要があります。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-the-application-gateway-configuration-objects"></a>アプリケーション ゲートウェイの構成オブジェクトの作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>手順 2.

**pool01** という名前のバックエンド IP アドレス プールを構成します。このとき、**pool01** 用の IP アドレスを使用します。 これらの IP アドレスは、アプリケーション ゲートウェイによって保護される Web アプリケーションをホストしているリソースの IP アドレスです。 これらのバックエンド プール メンバーはすべて、基本のプローブかカスタム プローブで正常性を検証されます。  そして、アプリケーション ゲートウェイに要求が届いたときに、これらのメンバーにトラフィックがルーティングされます。 アプリケーション ゲートウェイ内では、複数の規則を使ってバックエンド プールを使用できます。これは、1 つのバックエンド プールを、同じホスト上に存在する複数の Web アプリケーションのために使用できることを意味します。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

この例では、URL パスに基づいてネットワーク トラフィックをルーティングするためのバックエンド プールが 2 つあります。 1 つのプールは、URL パス "/video" からトラフィックを受信し、もう 1 つのプールはパス "/image" からトラフィックを受信します。 独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換えます。

### <a name="step-3"></a>手順 3.

バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、アプリケーション ゲートウェイの設定 **poolsetting01** を構成します。 各バックエンド プールは、独自のバックエンド プール設定を持つことができます。  バックエンド HTTP 設定は、適切なバックエンド プール メンバーにトラフィックをルーティングするための規則で使用されます。 バックエンド HTTP 設定では、バックエンド プール メンバーにトラフィックを送信する際に使用されるプロトコルとポートを決定します。 Cookie ベースのセッションを使用するかどうかも、この設定で決定します。  有効にした場合、Cookie ベースのセッション アフィニティは、各パケットに対する以前の要求と同じバックエンドにトラフィックを送信します。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>手順 4.

Application Gateway のフロント エンド ポートを構成します。 フロントエンド ポート構成オブジェクトはリスナーで使用されます。これを使用して、アプリケーション ゲートウェイのリスナーがトラフィックをリッスンするポートを定義します。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>手順 5.

パブリック IP エンドポイントでフロント エンド IP を構成します。 フロントエンド IP 構成オブジェクトはリスナーで使用されます。これを使用して、外部に接続する IP アドレスとリスナーを関連付けます。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>手順 6.

リスナーを構成します。 この手順では、着信ネットワーク トラフィックを受信するために使用するポートとパブリック IP アドレスについて、リスナーを構成します。 次の例では、先ほど構成したフロントエンド IP 構成、フロントエンド ポート構成、プロトコル (http または https) を使用して、リスナーを構成します。 この例のリスナーは、先ほど作成したパブリック IP アドレスを使ってポート 80 で HTTP トラフィックをリッスンします。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>手順 7.

ロード バランサーの動作を構成する、**rule01** という名前のロード バランサーのルーティング規則を作成します。 この規則には、これまでの手順で構成したバックエンド プール設定、リスナー、バックエンド プールを使用します。 定義した基準に基づいて、トラフィックが適切なバックエンドにルーティングされます。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>手順 8.

Application Gateway のインスタンス数とサイズを構成します。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> **InstanceCount** の既定値は 2、最大値は 10 です。 **GatewaySize** の既定値は Medium です。 **Standard_Small**、**Standard_Medium**、**Standard_Large** のいずれかを選択できます。

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

前の手順の構成項目をすべて使用して、アプリケーション ゲートウェイを作成します。 この例では、アプリケーション ゲートウェイは **appgwtest** という名前です。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

アプリケーション ゲートウェイに接続されているパブリック IP リソースから、アプリケーション ゲートウェイの DNS と VIP の詳細を取得します。

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Application Gateway の削除

Application Gateway を削除するには、次の手順を実行します。

### <a name="step-1"></a>手順 1

アプリケーション ゲートウェイ オブジェクトを取得し、変数 `$getgw` に関連付けます。

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>手順 2.

`Stop-AzureRmApplicationGateway` を使用してアプリケーション ゲートウェイを停止します。

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

アプリケーション ゲートウェイが停止状態になったら、`Remove-AzureRmApplicationGateway` コマンドレットを使用してサービスを削除します。

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> **-force** スイッチを使用すると、削除の確認メッセージを表示しないように設定できます。

サービスが削除されていることを確認するには、`Get-AzureRmApplicationGateway` コマンドレットを使用します。 この手順は必須ではありません。

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実に見つけられるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 次に、[Azure でのカスタム ドメイン名を構成します](../cloud-services/cloud-services-custom-domain-name-portal.md)。 動的に生成された DNS 名を探すには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したリソースをすべて削除するには、次の手順を実行します。

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>次のステップ

SSL オフロードを構成する場合は、[SSL オフロード用のアプリケーション ゲートウェイの構成](application-gateway-ssl.md)に関するページを参照してください。

内部ロード バランサーと共に使用するようにアプリケーション ゲートウェイを構成する場合は、「[内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散オプション全般の詳細については、次の記事をご覧ください。

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


