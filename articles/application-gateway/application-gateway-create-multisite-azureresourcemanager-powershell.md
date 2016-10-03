<properties
   pageTitle="複数のサイトをホストするアプリケーション ゲートウェイを作成する | Microsoft Azure"
   description="このページでは、同じゲートウェイで複数の Web アプリケーションをホストするための Azure アプリケーション ゲートウェイを作成して構成する手順について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>


# 複数の Web アプリケーションをホストするためのアプリケーション ゲートウェイを作成する

複数サイトのホストにより、複数の Web アプリケーションを同じアプリケーション ゲートウェイにデプロイすることができます。どのリスナーがトラフィックを受信するかを決定するには、受信 HTTP 要求にホスト ヘッダーがあるかどうかに依存します。リスナーは、ゲートウェイのルール定義の構成に従って、適切なバックエンド プールにトラフィックを送ります。SSL 対応の Web アプリケーションの場合、アプリケーション ゲートウェイは Server Name Indication (SNI) 拡張機能を使用して Web トラフィックに適切なリスナーを選択します。

複数サイトのホストの一般的な用途は、さまざまな Web ドメインに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。同様に、同じルート ドメインの複数のサブドメインも、同じアプリケーション ゲートウェイでホストすることができます。

## シナリオ
次の例では、アプリケーション ゲートウェイは、2 つのバックエンド サーバー プール (contoso サーバー プールと fabrikam サーバー プール) を使用して contoso.com および fabrikam.com のトラフィックを処理します。同様の設定は、app.contoso.com や blog.contoso.com などのサブドメインのホストにも使用できます。


## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。[ダウンロード ページ](https://azure.microsoft.com/downloads/)の **Windows PowerShell** セクションから最新バージョンをダウンロードしてインストールできます。
2. アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. Application Gateway を使用するためにバックエンド プールに追加されたサーバーが存在している必要があります。つまり、仮想ネットワーク内、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。



## Application Gateway の作成に必要な構成


- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。FQDN を使用することもできます。
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。複数サイト対応のアプリケーション ゲートウェイでは、ホスト名と SNI インジケーターも追加されます。
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## アプリケーション ゲートウェイの作成

Application Gateway を作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。
3. Application Gateway 構成オブジェクトを作成します。
4. Application Gateway のリソースを作成します。

## リソース マネージャーのリソース グループの作成

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

	Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### 手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-RG -location "East Asia"

あるいは、Application Gateway のリソース グループに対してタグを作成することもできます。
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "East Asia" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"} 

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-RG" という名前のリソース グループと "East Asia" という名前の場所を作成しました。

>[AZURE.NOTE] アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、[PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。詳細については、[カスタム プローブと正常性監視](application-gateway-probe-overview.md)に関するページを参照してください。

## アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### 手順 1

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 手順 2.

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ "appgw-rg" に、"appgwvnet" という名前の仮想ネットワークを作成します。

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "East Asia" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 手順 3.

Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -VirtualNetwork $vnet

## フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ "appgw-rg" に、パブリック IP リソース "publicIP01" を作成します。

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "East Asia" -AllocationMethod Dynamic

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## Application Gateway 構成の作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### 手順 1

"gatewayIP01" という名前の Application Gateway の IP 構成を作成します。アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。各インスタンスが IP アドレスを 1 つ取得することに注意してください。


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 手順 2.

"pool01" および "pool2" という名前のバックエンド IP アドレス プールを構成します。"pool1" には IP アドレス "10.0.0.100, 10.0.0.101,10.0.0.102" を使用し、"pool2" には IP アドレス "10.0.0.103, 10.0.0.104, 10.0.0.105" を使用します。


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.100, 10.0.0.101,10.0.0.102 
	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.0.103, 10.0.0.104, 10.0.0.105

この例では、要求されたサイトに基づいてネットワーク トラフィックをルーティングするためのバックエンド プールが 2 つあります。1 つのプールは、サイト "contoso.com" からトラフィックを受信し、もう 1 つのプールはサイト "fabrikam.com" からトラフィックを受信します。独自のアプリケーションの IP アドレス エンドポイントを追加するには、上記の IP アドレスを置き換える必要があります。内部 IP アドレスの代わりに、バックエンド インスタンスにパブリック IP アドレス、FQDN、または VM の NIC も使用できます。IP の代わりに FQDN を指定する場合は、PowerShell で "-BackendFQDNs" パラメーターを使用してください。

### 手順 3.

バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、Application Gateway の設定 "poolsetting01" と "poolsetting02" を構成します。この例では、各バックエンド プールに対してそれぞれ異なるバックエンド プール設定を構成します。各バックエンド プールは、独自のバックエンド プール設定を持つことができます。


	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### 手順 4.

パブリック IP エンドポイントでフロント エンド IP を構成します。

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### 手順 5.

Application Gateway のフロント エンド ポートを構成します。

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### 手順 6.

この例でサポートする 2 つの Web サイトのために、2 つの SSL 証明書を構成します。1 つの証明書が contoso.com トラフィック用で、もう 1 つが fabrikam.com トラフィック用です。これらの証明書は、Web サイトに対して証明機関によって発行された証明書である必要があります。自己署名証明書もサポートされていますが、運用環境のトラフィックにはお勧めしません。

	$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name  contosocert -CertificateFile <file path> -Password <password>
	$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### 手順 7.

この例では、2 つの Web サイトのために 2 つのリスナーを構成します。この手順では、着信トラフィックを受信するために使用するパブリック IP アドレス、ポート、およびホストのリスナーを構成します。複数サイトのサポートには HostName パラメーターが必要であり、トラフィックの受信対象である適切な Web サイトに設定する必要があります。複数ホストのシナリオで SSL のサポートを必要とする Web サイトでは、RequireServerNameIndication パラメーターを true に設定する必要があります。SSL のサポートが必要な場合は、その Web アプリケーションへのトラフィックをセキュリティで保護するために使用される SSL 証明書も指定する必要があります。FrontendIPConfiguration、FrontendPort、HostName の組み合わせは、リスナーに対して一意である必要があります。リスナーごとに 1 つの証明書をサポートできます。

	$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
	$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### 手順 8.

この例では、2 つの Web アプリケーション用に 2 つのルール設定を作成します。ルールによって、リスナー、バックエンド プール、および http 設定が相互に関連付けられます。この手順では、Web サイトごとに 1 つ、基本のルーティング ルールを使用するようにアプリケーション ゲートウェイを構成します。各 Web サイトへのトラフィックは、構成されたリスナーによって受信され、BackendHttpSettings で指定されたプロパティを使用して、構成されたバックエンド プールに送られます。

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
	$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### 手順 9.

Application Gateway のインスタンス数とサイズを構成します。

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## アプリケーション ゲートウェイの作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "East Asia" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02 	


>[AZURE.IMPORTANT] Application Gateway プロビジョニングは実行時間の長い操作であり、完了までに時間がかかる場合があります。

## アプリケーション ゲートウェイの DNS 名の取得

アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。
	
	Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01
		
	Name                     : publicIP01
	ResourceGroupName        : appgw-RG
	Location                 : eastasia
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

<!---HONumber=AcomDC_0921_2016-->