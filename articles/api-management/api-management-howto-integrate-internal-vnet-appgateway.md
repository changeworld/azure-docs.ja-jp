---
title: "仮想ネットワークで Application Gateway と Azure API Management を使用する方法 | Microsoft Docs"
description: "内部仮想ネットワークで Application Gateway (WAF) を使用して Azure API Management をセットアップおよび構成する方法の説明"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>仮想 VNET 内の API Management と Application Gateway の統合 

##<a name="overview"> </a> 概要
 
仮想ネットワークでは、API Management サービスを仮想ネットワーク内からのみアクセスできるようにする内部モードに構成できます。 Azure Application Gateway は、レイヤー&7; のロード バランサーを提供する PaaS サービスです。 リバースプロキシとしての役目を果たすものであり、その機能の&1; つとして Web アプリケーション ファイアウォール (WAF) を備えています。

内部 VNET にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。

* 内部コンシューマーと外部コンシューマーの両方における消費用に同一の API Management リソースを使用する。
* 単一の API Management リソースを使用しながら、API Management 内で定義した API の一部を外部コンシューマーが利用できるようにする。
* ターンキーを使用して、パブリック インターネットから API Management へのアクセスのオン/オフを切り替える。 

##<a name="scenario"> </a> シナリオ
この記事では、内部コンシューマーと外部コンシューマーの両方で単一の API Management サービスを使用し、オンプレミスとクラウド双方の API の単一フロントエンドとして機能させる方法について説明します。 さらに、Application Gateway の PathBasedRouting 機能を使用して、これらの API のうち外部消費用に公開するものを一部のみ (緑色で記載しています) に制限することもできます。

このセットアップでは、すべての API の管理は仮想ネットワークからのみ行います。 内部コンシューマー (オレンジ色で記載しています) は、内部 API と外部 API のすべてにアクセスできます。 トラフィックがインターネットへと出ていかないため、内部コンシューマーには ExpressRoute 回線により高速で通信できるというメリットがあります。

![url ルート](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. 仮想ネットワークを作成し、API Management と Application Gateway 用に別々のサブネットを作成します。 
3. 仮想ネットワーク用にカスタム DNS サーバーを作成する場合は、デプロイの開始前に仮想ネットワークの新しいサブネット内で仮想マシンを作成し、この仮想マシンがすべての Azure サービス エンドポイントを解決できるようにします。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management と Application Gateway の統合作成に必要なもの

* **バックエンド サーバー プール:** これは、API Management サービスの内部仮想 IP アドレスです。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。
* **カスタムの正常性プローブ:** 既定では、Application Gateway は IP ベースのプローブを使用して、BackendAddressPool が有効化されているサーバーを見つけます。 API Management サービスは適切なホスト ヘッダーを持つ要求だけに応答するため、既定のプローブでは失敗します。 Application Gateway に対し、サービスが有効であり要求の転送が必要であることを知らせるカスタムの正常性プローブを定義する必要があります。
* **カスタムのドメイン証明書:** インターネットから API Management にアクセスするには、ホスト名を CNAME で Application Gateway のフロントエンド DNS 名にマッピングする必要があります。 API Management が Application Gateway 経由での要求の受信時にその要求を有効であると認識して応答できるように、API Management に同一のホスト名のヘッダーと証明書を適用します。
## <a name="overview-steps"> </a> API Management と Application Gateway を統合するために必要な手順 

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。 API Management 用のサブネットを別途作成します。
3. 上記で作成した VNET サブネットの内部に、API Management サービスを内部 VNET モードで作成します。
4. カスタム ドメイン名で API Management サービスを更新します。
5. Application Gateway 構成オブジェクトを作成します。
6. Application Gateway のリソースを作成します。
7. API Management プロキシのホスト名を Application Gateway リソースのパブリック DNS 名に CNAME でマッピングします。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure へのログイン

```powershell
Login-AzureRmAccount
```

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認して選択します。

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### <a name="step-1"></a>手順 1

仮想ネットワークの作成時に Application Gateway 用に使用するサブネット変数に、アドレス範囲 10.0.0.0/24 を割り当てます。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>手順 2.

仮想ネットワークの作成時に API Management 用に使用するサブネット変数に、アドレス範囲 10.0.1.0/24 を割り当てます。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>手順 3.

サブネット 10.0.0.0/24 および 10.0.1.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **apim-appGw-RG** に **appgwvnet** という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>手順 4.

次の手順のためにサブネット変数を割り当てます。

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>内部 VNET モードでの API Management サービスの作成

次の例では、API Management サービスを内部 VNT 内に API Management サービスを作成する方法について説明します。

### <a name="step-1"></a>手順 1
上記手順で作成したサブネット $apimsubnetdata を使用して、API Management の仮想ネットワーク オブジェクトを作成します。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>手順 2.
仮想ネットワーク内に API Management サービスを作成します。

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
上記のコマンドが成功したら、[内部 VNET の API Management サービスへのアクセスに必要な DNS 構成][api-management-using-with-internal-vnet.md#apim-dns-configuration] を参照してアクセスします。

## <a name="update-api-management-service-with-custom-domain-name"></a>カスタム ドメイン名で API Management サービスを更新する

次は、インターネットからアクセスする API Management のサービス エンドポイント プロキシに、カスタム ドメイン名を適用します。

### <a name="step-1"></a>手順 1
ドメイン (ここでは `*.contoso.net` とします) でのカスタム ドメイン設定権限があることを示す証明書を、秘密キーとともにアップロードします。 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>手順 2.
`*.contoso.net` ドメインに対する権限を示す証明書をアップロードしたので、ホスト名に `api.contoso.net` を指定してプロキシのホスト名構成オブジェクトを作成します。 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ **apim-appGw-RGrg** に、パブリック IP リソース **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを&1; つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>手順 2.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>手順 3.

パブリック IP エンドポイントでフロント エンド IP を構成します。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>手順 4.

アプリケーション ゲートウェイの証明書を構成します。 この証明書は、アプリケーション ゲートウェイでのトラフィックの暗号化解除と再暗号化に使用されます。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>手順 5.

アプリケーション ゲートウェイの HTTP リスナーを作成します。 使用するフロントエンド IP 構成、ポート、および SSL 証明書を割り当てます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>手順 6.

この手順では、API Management サービスの `ContosoApi` プロキシのドメイン エンドポイントに対するカスタム プローブを作成します。 パス `/status-0123456789abcdef` は、すべての API Management サービスでホストされている既定の正常性エンドポイントです。 ホスト名 `contosoapi.azure-api.net` は、パブリック Azure でのサービス `contosoapi` の作成時に構成される既定のプロキシ ホスト名です。

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>手順 7.

SSL 対応バックエンド プール リソースで使用する証明書をアップロードします。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>手順 8.

バックエンド プール内のトラフィック用に Application Gateway の設定 **apimPoolSetting** を構成します。 また、この手順では、アプリケーション ゲートウェイの要求に対するバックエンド プールの応答のタイムアウトも構成します。 Application Gateway は、バックエンド応答がタイムアウト制限に達すると要求を取り消します。 この値は、プローブ チェックに対するバックエンド応答についてのみ適用されるプローブ タイムアウトとは異なります。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>手順 9.

**apimbackend** という名前のバックエンド IP アドレス プールを、上記で作成した API Management サービスの内部仮想 IP アドレスを指定して構成します。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>手順 10.
バックエンド プールの URL ルール パスを構成します。 API Management では、`Echo API (/echo/), Calculator API (/calc/) etc.` のように複数の API を構成し、インターネットからアクセスできる API を `Echo API` のみに制限することができます。 

次の例では、"/echo/" パスに対し、トラフィックを "apimProxyBackendPool" バックエンドにルーティングする単純なルールを作成します。

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

上記の手順により、Application Gateway によりパス "/echo" に対する要求のみが許可されるようになります。 インターネットからのアクセス時には、API Management で構成済みの別の API に要求を送信すると Application Gateway から 404 が返されます。 

### <a name="step-11"></a>手順 11.

ルール設定を作成します。 この手順では、URL パスベースのルーティングを使用する Application Gateway を構成します。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>手順 12.

Application Gateway のインスタンス数とサイズを構成します。 ここでは、API Management リソースのセキュリティを高めるために [WAF SKU][../application-gateway/application-gateway-webapplicationfirewall-overview.md] を使用しています。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>手順 13.

WAF のモードを "Prevention" に構成します。
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway の作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>API Management プロキシのホスト名を Application Gateway リソースのパブリック DNS 名に CNAME でマッピングする

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 フロントエンド IP CNAME レコードを構成するには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 Application Gateway の DNS 名を使用して、この DNS 名に対して上記で構成したプロキシ ホスト名 `api.contoso.net` を指す CNAME レコードを作成する必要があります。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> まとめ
VNET 内の Azure API Management サービスでは、単一のゲートウェイ インターフェイスにより、ホスト先がオンプレミスかクラウドかにかかわらずすべての API へのアクセスを管理できます。 API を使用するユーザーやその使用状況の詳細が得られます。 Application Gateway を API Management と統合することで、インターネット上でのアクセス権を付与する API をより柔軟に設定できるとともに、API Management インスタンスのフロントエンドとして Web アプリケーション ファイアウォールを利用できるようになります。

##<a name="next-steps"> </a> 次のステップ
* Azure Application Gateway の詳細を確認する
  * [Application Gateway の概要](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway の Web アプリケーション ファイアウォール](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* VNET 内の API Management の詳細を確認する
  * [VNET での API Management の使用](api-management-using-with-vnet.md)





<!--HONumber=Feb17_HO1-->


