---
title: "Web アプリケーション ファイアウォールの構成 - Azure Application Gateway | Microsoft Docs"
description: "この記事では、既存または新規の Application Gateway で Web アプリケーション ファイアウォールの使用を開始する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: dab489a1c9fb7d4a51b9ccbce543b209bec23575
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>新規または既存の Application Gateway における Web アプリケーション ファイアウォールの構成

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Web アプリケーション ファイアウォールが有効な Application Gateway を作成する方法、または Web アプリケーション ファイアウォールを既存の Application Gateway に追加する方法について説明します。

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」を参照してください。

以下の記事では、[Web アプリケーション ファイアウォールを既存の Application Gateway に追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法と [Web アプリケーション ファイアウォールを使用する Application Gateway を作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。

![シナリオのイメージ][scenario]

## <a name="waf-configuration-differences"></a>WAF の構成上の相違点

[PowerShell を使用した Application Gateway の作成](application-gateway-create-gateway-arm.md)に関するページをお読みであれば、Application Gateway を作成するときに構成する SKU の設定はご存じのとおりです。 WAF には、Application Gateway で SKU を構成するときに定義する追加の設定があります。 Application Gateway 自体に加える変更はありません。

| **設定** | **詳細**
|---|---|
|**SKU** |WAF が追加されていない通常の Application Gateway では、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** の各サイズがサポートされています。 WAF を導入すると、2 つの SKU、**WAF\_Medium** と **WAF\_Large** が追加されます。 小規模な Application Gateway では、WAF はサポートされていません。|
|**レベル** | 使用できる値は **Standard** または **WAF** です。 Web アプリケーション ファイアウォールを使用する場合は、 **WAF** を選択する必要があります。|
|**モード** | この設定は WAF のモードです。 使用できる値は **Detection** と **Prevention**です。 WAF を検出モードで設定すると、すべての脅威がログ ファイルに記録されます。 防止モードでは、検出モードと同様にイベントがログに記録されますが、攻撃者は Application Gateway から "403 許可されていません" 応答を受信します。|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>既存の Application Gateway に Web アプリケーション ファイアウォールを追加する

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

1. Azure アカウントにログインします。

    ```powershell
    Login-AzureRmAccount
    ```

2. このシナリオで使用するサブスクリプションを選択します。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Web アプリケーション ファイアウォールを追加するゲートウェイを取得します。

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

1. Web アプリケーション ファイアウォール SKU を構成します。 利用可能なサイズは、**WAF\_Large** と **WAF\_Medium** です。 Web アプリケーション ファイアウォールを使用する場合は、レベルが **WAF** である必要があり、SKU の設定時に容量を確認する必要があります。

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

1. 次の例で定義しているように、WAF の設定を構成します。

   **FirewallMode** の設定に利用できる値は、"Prevention" と "Detection"です。

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

1. 前の手順で定義した設定で Application Gateway を更新します。

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

このコマンドを実行すると、Web アプリケーション ファイアウォールによって Application Gateway が更新されます。 [Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照して、Application Gateway のログを表示する方法について理解してください。 WAF のセキュリティの性質により、ログを定期的に確認して Web アプリケーションのセキュリティ状況を把握する必要があります。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Web アプリケーション ファイアウォールのある Application Gateway を作成する

次の手順では、Web アプリケーション ファイアウォールのある Application Gateway を作成するプロセスを最初から最後まで実行します。

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

1. `Login-AzureRmAccount` を実行して Azure にログインすると、資格情報を使用して認証を行うように求めるメッセージが表示されます。

1. `Get-AzureRmSubscription` を実行して、アカウントのサブスクリプションを確認します。

1. 使用する Azure サブスクリプションを選択します。

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>リソース グループの作成

Application Gateway のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 Application Gateway を作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

前の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

> [!NOTE]
> Application Gateway のカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブと Application Gateway の作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、 [カスタム プローブと正常性監視](application-gateway-probe-overview.md) に関するページを参照してください。

### <a name="configure-virtual-network"></a>仮想ネットワークを構成する

Application Gateway には、専用のサブネットが必要です。 この手順では、10.0.0.0/16 のアドレス空間と 2 つのサブネット (1 つは Application Gateway、1 つはバックエンド プール メンバー) を持つ仮想ネットワークを作成します。

```powershell
# Create a subnet configuration object for the Application Gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for Application Gateway instances. With a smaller subnet, you may not be able to add more instance of your Application Gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the backend pool members subnet
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previous created subnets
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-public-ip-address"></a>パブリック IP アドレスを構成する

外部からの要求を処理するために、Application Gateway にはパブリック IP アドレスが必要です。 このパブリック IP アドレスには、Application Gateway で使用するように定義された `DomainNameLabel` は必要ありません。

```powershell
# Create a public IP address for use with the Application Gateway. Defining the domainnamelabel during creation is not supported for use with Application Gateway
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Application Gateway の構成

```powershell
# Create a IP configuration. This configures what subnet the Application Gateway uses. When Application Gateway starts, it picks up an IP address from the subnet configured and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a backend pool to hold the addresses or NICs for the application that Application Gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authenication certificate that will be used to communicate with the backend servers
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Conifugre the backend HTTP settings to be used to define how traffic is routed to the backend pool. The authenication certificate used in the previous step is added to the backend http settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a frontend port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a frontend IP configuration to associate the public IP address with the Application Gateway
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the Application Gateway. This certificate is used to decrypt and re-encrypt the traffic on the Application Gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the Application Gateway. Assign the front-end ip configuration, port, and ssl certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

#Create a load balancer routing rule that configures the load balancer behavior. In this example, a basic round robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the Application Gateway
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

#Configure the waf configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the Application Gateway utilizing all the previously created configuration objects
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 基本的な Web アプリケーション ファイアウォールの構成で作成された Application Gateway は、CRS 3.0 の保護で構成されています。

## <a name="get-application-gateway-dns-name"></a>Application Gateway の DNS 名の取得

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、Application Gateway には、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーが Application Gateway を確実に見つけられるように、CNAME レコードを使用して、Application Gateway のパブリック エンドポイントを参照できます。 次に、[Azure でのカスタム ドメイン名を構成します](../cloud-services/cloud-services-custom-domain-name-portal.md)。 別名を構成するには、Application Gateway に接続されている PublicIPAddress 要素を使用して、Application Gateway の詳細とそれに関連付けられている IP/DNS 名を取得します。 Application Gateway の DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。 Application Gateway の再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

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

## <a name="next-steps"></a>次のステップ

[Application Gateway の診断](application-gateway-diagnostics.md)に関するページにアクセスして、診断ログを構成する方法と、Web アプリケーション ファイアウォールで検出または防止されたイベントを記録する方法を確認します。

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

