<properties 
   pageTitle="Azure リソース マネージャーを使用して SSL オフロード用のアプリケーション ゲートウェイを構成する | Microsoft Azure"
   description="このページでは、Azure リソース マネージャーを使用して、SSL オフロード用のアプリケーション ゲートウェイを作成する方法について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Azure リソース マネージャーを使用して SSL オフロード用の Application Gateway を構成する

> [AZURE.SELECTOR]
-[Azure Classic Powershell](application-gateway-ssl.md)
-[Azure Resource Manager Powershell](application-gateway-ssl-arm.md)

 ゲートウェイで SSL セッションを停止するように Application Gateway を構成し、Web ファーム上で発生するコストのかかる SSL 暗号化解除タスクを回避することができます。また、SSL オフロードはフロントエンド サーバーのセットアップと Web アプリケーションの管理も簡素化します。


>[AZURE.IMPORTANT]Azure リソースを使用する前に、Azure は現在、リソース マネージャーのデプロイ モデルとクラシック デプロイ モデルの 2 種類を備えていることを理解しておくことが重要です。Azure リソースを使用する前に、必ず[デプロイ モデルとツール](azure-classic-rm.md)について理解しておいてください。この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。このドキュメントでは、Azure リソース マネージャーを使用したアプリケーション ゲートウェイの作成について説明します。クラシック デプロイ モデル バージョンを使用するには、「[クラシック デプロイメント モデルを使用して SSL オフロード用の Application Gateway を構成する](application-gateway-ssl.md)」を参照してください。



## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。[ダウンロード ページ](http://azure.microsoft.com/downloads/)の **Windows PowerShell** セクションから最新バージョンをダウンロードしてインストールできます。
2. アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。仮想マシンまたはクラウドのデプロイでサブネットを使用していないことを確認します。アプリケーション ゲートウェイそのものが、仮想ネットワークのサブネットに含まれている必要があります。
3. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。つまり、仮想ネットワーク内、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## アプリケーション ゲートウェイの作成に必要な構成
 

- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。 
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、アプリケーション ゲートウェイで開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。 
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。現在、*basic* ルールのみサポートされます。*basic* ルールは、ラウンド ロビンの負荷分散です。

**構成に関する追加の注意:**

SSL 証明書の構成では、**HttpListener** のプロトコルを *Https* (大文字小文字の区別あり) に変更する必要があります。**SslCertificate** 要素は、SSL 証明書用に構成された変数値を設定して、**HttpListener** に追加する必要があります。フロント エンド ポートは 443 に更新する必要があります。

**cookie ベースのアフィニティを有効にするには**: クライアント セッションからの要求が常に Web ファーム内の同じ VM に送られるようにアプリケーション ゲートウェイを構成できます。これはセッション cookie の挿入によって行われ、ゲートウェイがトラフィックを適切に送信できるようにします。cookie ベースのアフィニティを有効にするには、**BackendHttpSettings** 要素で **CookieBasedAffinity** を *Enabled* に設定します。

 
## 新しいアプリケーション ゲートウェイの作成

Azure クラシック デプロイ モデルと Azure リソース マネージャーの使用方法の違いは、構成が必要なアプリケーション ゲートウェイと項目を作成する順番にあります。

リソース マネージャーを使用すると、アプリケーション ゲートウェイを作成するすべての項目は個別に構成され、その後結合されてアプリケーション ゲートウェイのリソースを作成します。


ここでは、アプリケーション ゲートウェイを作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループの作成
2. アプリケーション ゲートウェイの仮想ネットワーク、サブネット、およびパブリック IP の作成
3. アプリケーション ゲートウェイの構成オブジェクトの作成
4. アプリケーション ゲートウェイのリソースの作成


## リソース マネージャーのリソース グループの作成

ARM コマンドレットを使用するように PowerShell モードを切り替えてください。詳細については、「[リソース マネージャーでの Windows Powershell の使用](powershell-azure-resource-manager.md)」をご覧ください。

### 手順 1.

		PS C:\> Login-AzureRmAccount



### 手順 2

アカウントのサブスクリプションを確認する

		PS C:\> get-AzureRmSubscription 

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### 手順 3. 

使用する Azure サブスクリプションを選択します。<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 手順 4.

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

## アプリケーション ゲートウェイの仮想ネットワークとサブネットの作成

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### 手順 1	
	
	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

### 手順 2	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ "appw-rg" に、"appgwvnet" という名前の仮想ネットワークを作成します。

### 手順 3.

	$subnet=$vnet.Subnets[0]

次の手順で、変数 $subnet にサブネット オブジェクトを割り当てます。
	
## フロントエンド構成のパブリック IP アドレスの作成

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

米国西部リージョンのリソース グループ "appw-rg" に、パブリック IP リソース "publicIP01" を作成します。


## アプリケーション ゲートウェイの構成オブジェクトの作成

### 手順 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

"gatewayIP01" という名前のアプリケーション ゲートウェイの IP 構成を作成します。アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。各インスタンスは、1 つの IP アドレスを取得することに注意してください。
 
### 手順 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

この手順は、IP アドレス "134.170.185.46、134.170.188.221、134.170.185.50" を使用して、"pool01" という名前のバックエンド IP アドレス プールを構成します。 これらは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスとなります。上記の例の IP アドレスを Web アプリケーション エンドポイントの IP アドレスに置き換えます。

### 手順 3.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、アプリケーション ゲートウェイの設定 "poolsetting01" を構成します。

### 手順 4.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

この例では、パブリック IP エンドポイントに対して、"frontendport01" という名前のフロントエンド IP ポートを構成します。

### 手順 5. 

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

SSL 接続に使用する証明書を構成します。証明書は、.pfx 形式で、4 ～ 12 文字のパスワードを使用する必要があります。

### 手順 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

"fipconfig01" という名前のフロントエンド IP 構成を作成し、このフロントエンド IP 構成にパブリック IP アドレスを関連付けます。

### 手順 7.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


"listener01" という名前のリスナーを作成し、フロントエンド IP 構成と証明書にフロントエンド ポートを関連付けます。

### 手順 8. 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

"rule01" という名前のロード バランサーのルーティング規則を作成し、ロード バランサーの動作を構成します。

### 手順 9.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

アプリケーション ゲートウェイのインスタンスのサイズを構成します。

>[AZURE.NOTE]*InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。Standard\_Small、Standard\_Medium、Standard\_Large のいずれかを選択できます。

## New-AzureApplicationGateway を使用したアプリケーション ゲートウェイの作成

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

前の手順の構成項目をすべて使用して、アプリケーション ゲートウェイを作成します。この例では、アプリケーション ゲートウェイは "appgwtest" という名前です。


## アプリケーション ゲートウェイの起動

ゲートウェイを構成したら、`Start-AzureRmApplicationGateway` コマンドレットを使用してゲートウェイを起動します。アプリケーション ゲートウェイの課金は、ゲートウェイが正常に起動された後に開始します。


**注:** `Start-AzureRmApplicationGateway` コマンドレットの実行には最大で 15 ～ 20 分かかる場合があります。

次の例では、アプリケーション ゲートウェイの名前は "appgwtest" で、リソース グループの名前は "app-rg" です。


### 手順 1

アプリケーション ゲートウェイ オブジェクトを取得し、変数 "$getgw" に関連付けます。
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### 手順 2
	 
`Start-AzureRmApplicationGateway` を使用して、Application Gateway を起動します。

	 Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	

## アプリケーション ゲートウェイの状態の確認

`Get-AzureRmApplicationGateway` コマンドレットを使用してゲートウェイの状態を確認します。前の手順で *Start-AzureApplicationGateway* が成功した場合、State は *Succeeded* になります。

	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
	Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## 次のステップ

ILB とともに使用するように Application Gateway を構成する場合は、「[内部ロード バランサー (ILB) を使用した Application Gateway の作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1203_2015-->