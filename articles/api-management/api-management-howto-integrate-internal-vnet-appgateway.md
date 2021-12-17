---
title: 仮想ネットワーク内の API Management を Azure Application Gateway と使用する方法
titleSuffix: Azure API Management
description: 内部仮想ネットワークに Azure API Management を設定して構成し、Application Gateway (Web Application Firewall) をフロントエンドとして設定する
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.author: danlep
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: 793216ac8c411fbde6db5d044d345f1de98af17a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710346"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>内部仮想ネットワーク内の API Management を Application Gateway と統合する

仮想ネットワーク内からのみアクセスできるように、API Management サービスを[内部モードの仮想ネットワーク](api-management-using-with-internal-vnet.md)内に構成することができます。 [Azure Application Gateway](../application-gateway/overview.md) は PaaS サービスであり、レイヤー 7 のロード バランサーの役目を果たします。 リバースプロキシ サービスとして機能し、そのオファリングの 1 つが Web アプリケーション ファイアウォール (WAF) です。

内部仮想ネットワーク内にプロビジョニングされた API Management を Application Gateway フロントエンドと組み合わせると、次のことが可能になります。

* 内部コンシューマーと外部コンシューマーの両方における消費用に同一の API Management リソースを使用する。
* 単一の API Management リソースを使用しながら、API Management 内で定義した API の一部を外部コンシューマーが利用できるようにする。
* パブリック インターネットから API Management へのアクセスのオン/オフを切り替えるターンキー方式を提供する。

> [!NOTE]
> この記事は、[Application Gateway WAF_v2 SKU](../application-gateway/application-gateway-autoscaling-zone-redundant.md) を使用する内容に更新されました。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事で説明されている手順に従うには、以下が必要です。

* 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 証明書
     - API Management サービスのカスタム ホスト名 (ゲートウェイ、開発者ポータル、管理エンドポイント) 用の PFX ファイル。 
     - PFX 証明書のルート証明書用の CER ファイル。 
     
    詳細については、[バックエンドの証明書](../application-gateway/certificates-for-backend-authentication.md)に関する記事を参照してください。 テスト目的で、必要に応じて[自己署名証明書](../application-gateway/self-signed-certificates.md)を生成します。
* Azure PowerShell の最新バージョン。 まだインストールしていない場合は、[Azure PowerShell をインストールします](/powershell/azure/install-az-ps)。

## <a name="scenario"></a>シナリオ

この記事では、単一の API Management サービスを内部と外部の両方のコンシューマーに使用して、オンプレミスとクラウドの両方の API の単一フロントエンドとして機能させる方法について説明します。 さらに、Application Gateway のルーティング機能を使用して、これらの API の一部のみ (この例では、緑色で表示) を外部コンシューマーに公開する方法について説明します。

最初の設定例では、すべての API を仮想ネットワーク内からのみ管理します。 内部コンシューマー (オレンジ色で記載しています) は、内部 API と外部 API のすべてにアクセスできます。 トラフィックがインターネットに送信されることはありません。 ハイ パフォーマンス接続を Express Route 回線を介して提供できます。

![url ルート](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>API Management と Application Gateway の統合に必要なもの

* **バックエンド サーバー プール:** これは、API Management サービスの内部仮想 IP アドレスです。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。 これらの設定は、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** これは、Application Gateway でオープンしているパブリック ポートです。 このポートにヒットしたトラフィックは、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、TLS/SSL 証明書名 (TLS オフロードを構成する場合) があります。
* **ルール:** ルールを使って、リスナーをバックエンド サーバー プールにバインドします。
* **カスタムの正常性プローブ:** BackendAddressPool 内のアクティブなサーバーを見つけるため、既定では、IP アドレス ベースのプローブが Application Gateway によって使用されます。 API Management サービスは適切なホスト ヘッダーを持つ要求だけに応答するため、既定のプローブでは失敗します。 カスタムの正常性プローブを定義して、サービスが有効であり、要求を転送する必要があることをアプリケーション ゲートウェイで判定できるようにします。
* **カスタム ドメイン証明書:** インターネットから API Management にアクセスするには、ホスト名と Application Gateway のフロントエンド DNS 名との間の CNAME マッピングを作成する必要があります。 これにより、Application Gateway に送信され、API Management に転送されるホスト名のヘッダーと証明書が、API Management によって有効であると認識されるものであることが保証されます。 この例では、API Management サービスのゲートウェイ (バックエンド)、開発者ポータル、および管理エンドポイントに対して 3 つの証明書を使用します。  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>API Management と Application Gateway を統合するために必要な手順

1. リソース マネージャーのリソース グループを作成します。
1. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。 API Management 用のサブネットを別途作成します。
1. 前の手順で作成した仮想ネットワーク サブネット内に API Management サービスを作成します。 内部モードを使用していることを確認します。
1. API Management サービスでカスタム ドメイン名を設定します。
1. 仮想ネットワーク内に DNS 解決用のプライベート DNS ゾーンを構成します。
1. Application Gateway 構成オブジェクトを作成します。
1. Application Gateway のリソースを作成します。
1. Application Gateway のパブリック DNS 名から API Management プロキシのホスト名への CNAME を作成します。

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>Application Gateway を介して開発者ポータルと管理エンドポイントを外部に公開する

このガイドでは、アプリケーション ゲートウェイを介して、**開発者ポータル** と **管理エンドポイント** を外部の対象ユーザーにも公開します。 各エンドポイントのリスナー、プローブ、設定、ルールを作成するために追加の手順が必要です。 詳細はすべて、それぞれの手順で示されます。

> [!WARNING]
> Azure AD またはサード パーティの認証を使用する場合は、Application Gateway で [Cookie ベースのセッション アフィニティ](../application-gateway/features.md#session-affinity)機能を有効にしてください。

> [!WARNING]
> 開発者ポータルで行う OpenAPI 仕様のダウンロードが Application Gateway WAF によって中断されないようにするには、ファイアウォール規則 `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` を無効にする必要があります。
> 
> 以下の Application Gateway WAF ルールは、ポータルの機能を中断させる可能性があります。
> 
> - 管理モード用: `920300`、`920330`、`931130`、`942100`、`942110`、`942180`、`942200`、`942260`、`942340`、および `942370`
> - 発行されたポータル用: `942200`、`942260`、`942370`、`942430`、および `942440`

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

### <a name="step-1"></a>手順 1

Azure にログインする

```powershell
Connect-AzAccount
```

資格情報を使用して認証を行います。

### <a name="step-2"></a>手順 2.

目的のサブスクリプションを選択します。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>手順 3.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例は、Resource Manager を使用して仮想ネットワークを作成する方法を示しています。 この例の仮想ネットワークは、Application Gateway と API Management の別々のサブネットで構成されています。

### <a name="step-1"></a>Step 1

Application Gateway と API Management のサブネットにネットワーク セキュリティ グループと NSG ルールを作成します。

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwRule2 = New-AzNetworkSecurityRuleConfig -Name appgw-in-internet -Description "AppGw inbound Internet" `
    -Access Allow -Protocol "TCP" -Direction Inbound -Priority 110 -SourceAddressPrefix `
    Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 443
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1, $appGwRule2

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>手順 2

仮想ネットワークの作成時に、Application Gateway に使用するサブネット変数にアドレス範囲 10.0.0.0/24 を割り当てます。

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>手順 3

仮想ネットワークの作成時に、API Management に使用するサブネット変数にアドレス範囲 10.0.1.0/24 を割り当てます。

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>手順 4

米国西部リージョンのリソース グループ **apim-appGw-RG** に **appgwvnet** という名前の仮想ネットワークを作成します。 サブネット 10.0.0.0/24 および 10.0.1.0/24 と共に、プレフィックス 10.0.0.0/16 を使用します。

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>手順 5.

次の手順で使用できるようにサブネット変数を割り当てます。

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>内部モードで構成した仮想ネットワーク内に API Management サービスを作成する

次の例は、内部アクセス専用に構成した仮想ネットワーク内に API Management サービスを作成する方法を示しています。

### <a name="step-1"></a>Step 1

前述の手順で作成したサブネット `$apimSubnetData` を使用して、API Management の仮想ネットワーク オブジェクトを作成します。

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>手順 2

仮想ネットワーク内に API Management サービスを作成します。 この例では、Developer サービス レベルにサービスを作成します。 ご自分の API Management サービスに一意の名前を設定します。

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

このレベルに API Management サービスを作成してアクティブにするには、30 から 40 分かかる場合があります。 上記のコマンドが成功したら、[内部仮想ネットワークの API Management サービスにアクセスするために必要な DNS の構成](api-management-using-with-internal-vnet.md#dns-configuration)に関する記事を参照して、アクセスを確認します。 

## <a name="set-up-custom-domain-names-in-api-management"></a>API Management でカスタム ドメイン名を設定する

### <a name="step-1"></a>Step 1

ドメインの秘密キーと信頼されたルート証明書を含む証明書の詳細を使用して以下の変数を初期化します。 この例では、`api.contoso.net`、`portal.contoso.net`、`management.contoso.net` を使用します。  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>手順 2

API Management エンドポイントに対して、ホスト名構成オブジェクトを作成し、設定します。  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> 従来の開発者ポータルの接続を構成するには、`-HostnameType DeveloperPortal` を `-HostnameType Portal` に置き換える必要があります。

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>仮想ネットワークで DNS 解決用のプライベート ゾーンを構成する

### <a name="step-1"></a>Step 1

プライベート DNS ゾーンを作成し、仮想ネットワークをリンクします。

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>手順 2

カスタム ドメイン ホスト名の A レコードを作成して、API Management サービスのプライベート IP アドレスにマッピングします。

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

リソース グループに標準パブリック IP リソースの **publicIP01** を作成します。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 Application Gateway が起動すると、IP アドレスが構成済みのサブネットから取得され、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>手順 2.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>手順 3.

パブリック IP エンドポイントでフロント エンド IP を構成します。

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>手順 4.

通過するトラフィックの暗号化解除と再暗号化に使用される、Application Gateway 用に証明書を構成します。

> [!NOTE]
> Application Gateway では、カスタム TLS オプションの定義、特定の TLS プロトコル バージョンの無効化、暗号スイートと優先順序の指定がサポートされています。 構成できる TLS オプションの詳細については、[TLS ポリシーの概要](../application-gateway/application-gateway-ssl-policy-overview.md)に関するページを参照してください。

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>手順 5.

Application Gateway 用の HTTP リスナーを作成します。 フロントエンド IP 構成、ポート、および TLS/SSL 証明書をリスナーに割り当てます。

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>手順 6.

API Management サービスの `ContosoApi` ゲートウェイ ドメイン エンドポイントに対するカスタム プローブを作成します。 パス `/status-0123456789abcdef` は、すべての API Management サービスでホストされている既定の正常性エンドポイントです。 `api.contoso.net` をカスタム プローブのホスト名として設定し、TLS/SSL 証明書でセキュリティ保護します。

> [!NOTE]
> ホスト名 `contosoapi.azure-api.net` は、`contosoapi` という名前のサービスがパブリック Azure に作成されたときに構成された既定のプロキシ ホスト名です。
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>手順 7.

HTTP 設定で構成できるように、信頼されたルート証明書をアップロードします。

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>手順 8.

バックエンド要求のタイムアウト制限 (これを過ぎると、キャンセルされる) を含め、Application Gateway の HTTP バックエンド設定を構成します。 この値はプローブのタイムアウトとは異なります。

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>手順 9.

API Management エンドポイントごとに、それぞれのドメイン名を使用して、バックエンド IP アドレス プールを構成します。

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>手順 10.

基本のルーティングを使用するように、アプリケーション ゲートウェイのルールを作成します。

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> 開発者ポータルの特定ページへのアクセスを制限するには、`-RuleType` とルーティングを変更します。

### <a name="step-11"></a>手順 11.

Application Gateway のインスタンス数とサイズを構成します。 この例では、API Management リソースのセキュリティを強化するために、[WAF_v2 SKU](../web-application-firewall/ag/ag-overview.md) を使用しています。

運用環境のワークロードには少なくとも 2 つのインスタンス (_Capacity_) を使用することをお勧めします。 ただし、非運用環境シナリオまたは一般的な実験には、1 つのインスタンスのみを使用することをお勧めします。 詳しくは、「[Azure Application Gateway の価格](../application-gateway/understanding-pricing.md#instance-count)」を参照してください。

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>手順 12.

WAF を "Prevention" モードに構成します。

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-13"></a>手順 13.

現在、TLS 1.0 が既定値であるため、最新の [TLS1.2 ポリシー](../application-gateway/application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)を使用するようにアプリケーション ゲートウェイを設定することをお勧めします。

```powershell
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
```

## <a name="create-application-gateway"></a>Application Gateway の作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe `
  -SslPolicy $policy
```

アプリケーション ゲートウェイのデプロイが完了したら、API Management バックエンドの正常性状態をポータルで確認、または次のコマンドを実行します。

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

各バックエンド プールの正常性状態が正常であることを確認します。 異常なバックエンドまたは正常性状態が不明のバックエンドのトラブルシューティングを行う必要がある場合は、「[Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング](../application-gateway/application-gateway-backend-health-troubleshooting.md)」を参照してください。

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management プロキシのホスト名から Application Gateway リソースのパブリック DNS 名への CNAME を作成する

ゲートウェイを作成したら、通信用にフロントエンドを構成します。 パブリック IP アドレスを使用する場合、Application Gateway は動的に割り当てられる DNS 名を必要としますが、これを使用するのは必ずしも簡単ではありません。

Application Gateway の DNS 名を使用して、API Management ゲートウェイ ホスト名 (前の例では `api.contoso.net`) がこの DNS 名を指すように CNAME レコードを作成します。 フロントエンド IP CNAME レコードを構成するには、`PublicIPAddress` 要素を使用して、Application Gateway の詳細とそれに関連付けられている IP/DNS 名を取得します。 ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

テスト目的で、ローカル コンピューター上の hosts ファイルを更新して、Application Gateway のパブリック IP アドレスを、構成した API Management エンドポイントの各ホスト名 (`api.contoso.net`、`portal.contoso.net`、`management.contoso.net` など) にマッピングするエントリを設定できます。

## <a name="summary"></a>まとめ

Azure API Management を仮想ネットワーク内に構成すると、ホスト先がオンプレミスかクラウドかにかかわらず、すべての構成済みの API に単一のゲートウェイ インターフェイスを提供できます。 Application Gateway を API Management と統合すると、インターネット上で特定の API に選択的にアクセスでき、API Management インスタンスのフロントエンドとして WAF を提供する柔軟性を提供できます。

## <a name="next-steps"></a>次のステップ

* Azure Application Gateway の詳細を確認する
  * [Application Gateway の概要](../application-gateway/overview.md)
  * [Application Gateway の Web アプリケーション ファイアウォール](../web-application-firewall/ag/ag-overview.md)
  * [パスベースのルーティングを使用して Application Gateway を作成する](../application-gateway/tutorial-url-route-powershell.md)
* API Management と仮想ネットワークの詳細を確認する
  * [内部仮想ネットワークで Azure API Management を使用する](api-management-using-with-internal-vnet.md)
  * [仮想ネットワークで API Management を使用する方法](api-management-using-with-vnet.md)
