<properties
   pageTitle="新規または既存の Application Gateway における Web アプリケーション ファイアウォールの構成 | Microsoft Azure"
   description="この記事では、既存または新規のアプリケーション ゲートウェイで Web アプリケーション ファイアウォールの使用を開始する方法について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace"/>

# 新規または既存の Application Gateway における Web アプリケーション ファイアウォールの構成

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-web-application-firewall-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure Application Gateway はレイヤー 7 のロード バランサーです。クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。サポートされている機能の完全な一覧については、「Application Gateway の概要」を参照してください。

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。

以下の記事では、[Web アプリケーション ファイアウォールを既存のアプリケーション ゲートウェイに追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法と [Web アプリケーション ファイアウォールを使用するアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。

![シナリオのイメージ][scenario]

## WAF の構成上の相違点

[PowerShell を使用した Application Gateway の作成](application-gateway-create-gateway-arm.md)に関するページをお読みであれば、アプリケーション ゲートウェイを作成するときに構成する SKU の設定はご存じのとおりです。WAF には、アプリケーション ゲートウェイで SKU を構成するときに定義する追加の設定があります。アプリケーション ゲートウェイ自体に加える変更はありません。

**SKU** -WAF が追加されていない通常のアプリケーション ゲートウェイでは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** の各サイズがサポートされています。WAF を導入すると、2 つの SKU、**WAF\_Medium** と **WAF\_Large** が追加されます。小規模なアプリケーション ゲートウェイでは、WAF はサポートされていません。

**層** - 使用できる値は **Standard** または **WAF** です。Web アプリケーション ファイアウォールを使用する場合は、**WAF** を選択する必要があります。

**モード** - WAF のモードです。使用できる値は、**検出**と**防止**です。WAF を検出モードで設定すると、すべての脅威がログ ファイルに記録されます。防止モードでは、検出モードと同様にイベントがログに記録されますが、攻撃者はアプリケーション ゲートウェイから "403 許可されていません" 応答を受信します。

## 既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

Azure PowerShell の最新バージョンを使用していることを確認します。詳細については、[Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### 手順 1

Azure アカウントにログインします。

    Login-AzureRmAccount

### 手順 2.

このシナリオで使用するサブスクリプションを選択します。

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### 手順 3.

Web アプリケーション ファイアウォールを追加するゲートウェイを取得します。

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### 手順 4.

Web アプリケーション ファイアウォール SKU を構成します。利用可能なサイズは、**WAF\_Large** と **WAF\_Medium** です。Web アプリケーションを使用するとき、層を **WAF** にする必要があります。

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF

### 手順 5.

次の例で定義しているように、WAF の設定を構成します。

**WafMode** の設定に利用できる値は、"Prevention" と "Detection"です。

    $config = Add-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention" -ApplicationGateway $gw

### 手順 6.

前の手順で定義した設定でアプリケーション ゲートウェイを更新します。

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

このコマンドを実行すると、Web アプリケーション ファイアウォールによってアプリケーション ゲートウェイが更新されます。[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照して、アプリケーション ゲートウェイのログを表示する方法について理解することをお勧めします。WAF のセキュリティの性質により、ログを定期的に確認して Web アプリケーションのセキュリティ状況を把握する必要があります。

## Web アプリケーション ファイアウォールのある Application Gateway を作成する

次の手順では、Web アプリケーション ファイアウォールのある Application Gateway を作成するプロセスを最初から最後まで実行します。

Azure PowerShell の最新バージョンを使用していることを確認します。詳細については、[Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### 手順 1

Azure へのログイン

	Login-AzureRmAccount

資格情報を使用して認証を行うように求めるメッセージが表示されます。

### 手順 2.

アカウントのサブスクリプションを確認します。

	Get-AzureRmSubscription

### 手順 3.

使用する Azure サブスクリプションを選択します。

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### 手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

前の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

>[AZURE.NOTE] アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、[PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。詳細については、[カスタム プローブと正常性監視](application-gateway-probe-overview.md)に関するページを参照してください。

### 手順 5.

Application Gateway 自体に使用するサブネットのアドレス範囲を割り当てます。

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] アプリケーションのサブネットには、少なくとも 28 個のマスク ビットが必要です。これによって、Application Gateway インスタンスのサブネットに 10 個の使用可能なアドレスが残ります。これより小さいサブネットには、今後アプリケーション ゲートウェイのインスタンスを追加することができません。

### 手順 6.

バックエンド アドレス プールに使用するアドレス範囲を割り当てます。

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### 手順 7.

[リソース グループの作成](#create-the-resource-group)手順で作成したリソース グループに、前述のサブネットを含む仮想ネットワークを作成します。

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### 手順 8.

次の手順で、使用する仮想ネットワーク リソースとサブネットのリソースを取得します。

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### 手順 9.

アプリケーション ゲートウェイに使用するパブリック IP リソースを作成します。このパブリック IP アドレスは、以降の手順のいずれかで使用します。

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application Gateway では、ドメイン ラベルを定義して作成したパブリック IP アドレスを使用できません。動的に作成されるドメイン ラベルを持つパブリック IP アドレスのみがサポートされています。アプリケーション ゲートウェイにわかりやすい DNS 名を付ける必要がある場合は、CNAME レコードをエイリアスとして使用することをお勧めします。

### 手順 10.

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

アプリケーション ゲートウェイの IP 構成を作成します。これにより、Application Gateway で使用するサブネットが構成されます。Application Gateway が起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。各インスタンスが IP アドレスを 1 つ取得することに注意してください。

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### 手順 11.

バックエンド Web サーバーの IP アドレスを使用してバックエンド IP アドレス プールを構成します。これらの IP アドレスは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。独自のアプリケーションの IP アドレス エンドポイントを追加するには、次の IP アドレスを置き換えます。

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### 手順 12.

アプリケーション ゲートウェイのバックエンドの http 設定を構成します。前の手順でアップロードした証明書を http 設定に割り当てます。

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### 手順 13.

パブリック IP エンドポイント用のフロントエンド IP ポートを構成します。このポートは、エンド ユーザーが接続するポートです。

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### 手順 14.

フロントエンドの IP 構成を作成します。この設定によって、アプリケーション ゲートウェイのフロントエンドにプライベート IP アドレスまたはパブリック IP アドレスがマッピングされます。次の手順で、フロントエンドの IP 構成と前の手順のパブリック IP アドレスを関連付けます。

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### 手順 15.

アプリケーション ゲートウェイの HTTP リスナーを作成します。使用するフロントエンド IP 構成、ポート、および SSL 証明書を割り当てます。

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### 手順 16.

ロード バランサーの動作を構成するロード バランサーのルーティング規則を作成します。この例では、基本的なラウンド ロビン規則を作成します。

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### 手順 17.

アプリケーション ゲートウェイのインスタンスのサイズを構成します。

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  **WAF\_Medium** と **WAF\_Large** から選択できます。WAF を使用するときの層は常に **WAF** です。Capacity に指定できるのは、1 から 10 までの任意の数です。

### 手順 18.

WAF のモードを構成します。指定できる値は **Prevention** と **Detection** です。

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### 手順 19.

前の手順の構成項目をすべて使用して、アプリケーション ゲートウェイを作成します。この例では、アプリケーション ゲートウェイは "appgwtest" という名前です。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WafConfig $config

## 次のステップ

[Application Gateway の診断](application-gateway-diagnostics.md)に関するページにアクセスして、診断ログを構成する方法と、Web アプリケーション ファイアウォールで検出または防止されたイベントを記録する方法を確認します。


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

<!---HONumber=AcomDC_0928_2016-->