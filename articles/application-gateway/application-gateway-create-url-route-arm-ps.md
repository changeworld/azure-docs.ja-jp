<properties
   pageTitle="URL ルーティング ルールを使用してApplication Gateway を作成する | Microsoft Azure"
   description="このページでは、URL ルーティング ルールを使用して Azure Application Gateway を作成し、構成する方法について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# URL ベースのルーティングを使用して Application Gateway を作成する 

URL パス ベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートの関連付けを行うことができます。Application Gateway の URL 一覧に対して構成されたバックエンド プールへのルートがあるかどうかを調べ、定義されたバックエンド プールにネットワーク トラフィックを送信します。URL ベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

URL ベースのルーティングでは、新しいルールの種類が Application Gateway に導入されています。Application Gateway には、basic と PathBasedRouting という 2 つのルールの種類があります。basic は、バックエンド プールに対してラウンドロビン サービスを提供します。一方、PathBasedRouting はラウンドロビン サービスに加えて、バックエンド プールを選択しながら要求 URL のパス パターンも考慮に入れます。

>[AZURE.IMPORTANT] PathPattern: これは照合するパス パターンの一覧です。それぞれ / で始まる必要があり、* が許可されるのは末尾の '/' の後だけです。パス照合に提供する文字列には最初の ? または # の後にテキストを含めません (これらの文字は、許可されません)。

## シナリオ
次の例で、Application Gateway は、2 つのバックエンド サーバー プール (ビデオ サーバー プールとイメージ サーバー プール) を使用して contoso.com のトラフィックを処理します。

http://contoso.com/image* に対する要求はイメージ サーバー プール (pool1) にルーティングされ、http://contoso.com/video* に対する要求はビデオ サーバー プール (pool2) にルーティングされます。一致するパス パターンがない場合は、既定のサーバー プール (pool1) が選択されます。

![url ルート](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。[ダウンロード ページ](https://azure.microsoft.com/downloads/)の **Windows PowerShell** セクションから最新バージョンをダウンロードしてインストールできます。
2. Application Gateway の仮想ネットワークとサブネットを作成します。仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. Application Gateway を使用するためにバックエンド プールに追加されたサーバーが存在している必要があります。つまり、仮想ネットワーク内、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。



## Application Gateway の作成に必要な構成


- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## 新しいアプリケーション ゲートウェイの作成

Azure クラシックと Azure リソース マネージャーの使用方法の違いは、設定が必要な Application Gateway と項目を作成する順番にあります。

リソース マネージャーを使用すると、Application Gateway を作成するすべての項目は個別に構成され、その後結合されて Application Gateway のリソースを作成します。


Application Gateway を作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。
3. Application Gateway 構成オブジェクトを作成します。
4. Application Gateway のリソースを作成します。

## リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。詳細については、「[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。

### 手順 1.
Azure Login-AzureRmAccount へのログイン

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### 手順 2.
アカウントのサブスクリプションを確認します。

		Get-AzureRmSubscription

### 手順 3.
使用する Azure サブスクリプションを選択します。<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### 手順 4.
新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
あるいは、Application Gateway のリソース グループに対してタグを作成することもできます。
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。Application Gateway を作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

>[AZURE.NOTE] Application Gateway のカスタム プローブを構成する必要がある場合は、「[PowerShell を使用して Azure Application Gateway (クラシック) のカスタム プローブを作成する](application-gateway-create-probe-ps.md)」を参照してください。詳細については、「[Application Gateway による正常性監視の概要](application-gateway-probe-overview.md)」を参照してください。

## Application Gateway の仮想ネットワークとサブネットの作成

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### 手順 1.
アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 手順 2.
サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ "appgw-rg" に、"appgwvnet" という名前の仮想ネットワークを作成します。

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 手順 3.
Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

	$subnet=$vnet.Subnets[0]

## フロントエンド構成のパブリック IP アドレスの作成
米国西部リージョンのリソース グループ "appgw-rg" に、パブリック IP リソース "publicIP01" を作成します。

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## Application Gateway 構成の作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### 手順 1.
"gatewayIP01" という名前の Application Gateway の IP 構成を作成します。Application Gateway が起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。各インスタンスは、1 つの IP アドレスを取得することに注意してください。


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 手順 2.
"pool01" および "pool2" という名前のバックエンド IP アドレス プールを構成します。"pool1" には IP アドレス "134.170.185.46, 134.170.188.221,134.170.185.50" を使用し、"pool2" には IP アドレス "134.170.186.46, 134.170.189.221,134.170.186.50" を使用します。


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

この例では、URL パスに基づいてネットワーク トラフィックをルーティングするためのバックエンド プールが 2 つあります。1 つのプールは、URL パス "/video" からトラフィックを受信し、もう 1 つのプールはパス "/image" からトラフィックを受信します。独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換える必要があります。

### 手順 3.
バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、Application Gateway の設定 "poolsetting01" と "poolsetting02" を構成します。この例では、各バックエンド プールに対してそれぞれ異なるバックエンド プール設定を構成します。各バックエンド プールは、独自のバックエンド プール設定を持つことができます。

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### 手順 4.
パブリック IP エンドポイントでフロント エンド IP を構成します。

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### 手順 5. 
Application Gateway のフロント エンド ポートを構成します。

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### 手順 6.
リスナーを構成します。この手順では、着信ネットワーク トラフィックを受信するために使用するポートとパブリック IP アドレスについて、リスナーを構成します。
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### 手順 7. 
バックエンド プールの URL ルール パスを構成します。この手順では、Application Gateway が使用する相対パスを構成します。これにより、URL パスと、着信トラフィックを処理するために割り当てられるバックエンド プールとの間のマッピングが定義されます。

次の例では 2 つのルールを作成します。1 つは、トラフィックをバックエンド "pool1" にルーティングする "/image/" パス用で、もう 1 つは、トラフィックをバックエンド "pool2" にルーティングする "/video/" パス用です。
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

パスがあらかじめ定義されているパス ルールのいずれとも一致しない場合、ルール パス マップ構成では、既定のバックエンド アドレス プールも構成します。

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### 手順 8. 
ルール設定を作成します。この手順では、URL パス ベースのルーティングを使用する Application Gateway を構成します。

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### 手順 9. 
Application Gateway のインスタンス数とサイズを構成します。

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Application Gateway の作成
前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Application Gateway の取得
	$getgw =  Get-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname

<!---HONumber=AcomDC_0427_2016-->