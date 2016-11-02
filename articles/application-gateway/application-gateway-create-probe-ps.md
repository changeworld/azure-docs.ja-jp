<properties
   pageTitle="リソース マネージャーで PowerShell を使用して Application Gateway のカスタム プローブを作成する | Microsoft Azure"
   description="リソース マネージャーで PowerShell を使用して、Application Gateway のカスタム プローブを作成する方法の説明"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# Azure リソース マネージャーで PowerShell を使用して Azure Application Gateway のカスタム プローブを作成する

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-create-probe-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### 手順 1

Login-AzureRmAccount を使用して認証を行います。

	Login-AzureRmAccount

### 手順 2.

アカウントのサブスクリプションを確認します。

	Get-AzureRmSubscription

### 手順 3.

使用する Azure サブスクリプションを選択します。<BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

## アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の手順では、アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

### 手順 1


アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### 手順 2.

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ "appgw-rg" に、"appgwvnet" という名前の仮想ネットワークを作成します。

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 手順 3.

Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

	$subnet = $vnet.Subnets[0]

## フロントエンド構成のパブリック IP アドレスの作成


米国西部リージョンのリソース グループ "appgw-rg" に、パブリック IP リソース "publicIP01" を作成します。

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## カスタム プローブを設定したアプリケーション ゲートウェイ構成オブジェクトの作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定します。次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### 手順 1

"gatewayIP01" という名前の Application Gateway の IP 構成を作成します。アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。各インスタンスが IP アドレスを 1 つ取得することに注意してください。

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 手順 2.


この手順は、IP アドレス "134.170.185.46, 134.170.188.221,134.170.185.50" を使用して、"pool01" という名前のバックエンド IP アドレス プールを構成します。これらの値は、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換えます。

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### 手順 3.


この手順では、カスタム プローブを作成します。

使用するパラメーターは次のとおりです。

- **Interval** - プローブのチェック間隔を秒単位で指定します。
- **Timeout** - プローブの HTTP 応答チェックのタイムアウト期間を定義します。
- **Hostname と Path** - インスタンスの状態を判断するために Application Gateway により呼び出される完全な URL パス。たとえば、http://contoso.com/ という Web サイトがある場合、HTTP 応答が正常かどうかをプローブでチェックするためには、カスタム プローブを "http://contoso.com/path/custompath.htm" に対して構成します。
- **UnhealthyThreshold** - バックエンド インスタンスに "*異常*" というフラグを設定するために必要な HTTP 応答の失敗数。

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### 手順 4.

バックエンド プール内のトラフィックに対して、アプリケーション ゲートウェイの設定 "poolsetting01" を構成します。また、この手順では、アプリケーション ゲートウェイの要求に対するバックエンド プールの応答のタイムアウトも構成します。Application Gateway は、バックエンド応答がタイムアウト制限に達すると要求を取り消します。この値は、プローブ チェックに対するバックエンド応答についてのみ適用されるプローブ タイムアウトとは異なります。

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### 手順 5.

パブリック IP エンドポイントに対して、"frontendport01" という名前のフロントエンド IP ポートを構成します。

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### 手順 6.

"fipconfig01" という名前のフロントエンド IP 構成を作成し、このフロントエンド IP 構成にパブリック IP アドレスを関連付けます。


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### 手順 7.

"listener01" という名前のリスナーを作成し、フロントエンド IP 構成にフロントエンド ポートを関連付けます。

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 手順 8.

ロード バランサーの動作を構成する "rule01" という名前のロード バランサーのルーティング規則を作成します。

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 手順 9.

アプリケーション ゲートウェイのインスタンスのサイズを構成します。

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  *InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。Standard\_Small、Standard\_Medium、Standard\_Large のいずれかを選択できます。

## New-AzureRmApplicationGateway を使用した Application Gateway の作成

前の手順の構成項目をすべて使用して、Application Gateway を作成します。この例では、アプリケーション ゲートウェイは "appgwtest" という名前です。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## 既存のアプリケーション ゲートウェイへのプローブの追加

既存のアプリケーション ゲートウェイにカスタム プローブを追加するには、次の 4 つの手順を実行します。

### 手順 1

**Get-AzureRmApplicationGateway** を使用して、PowerShell 変数にアプリケーション ゲートウェイのリソースを読み込みます。

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 手順 2.

既存のゲートウェイの構成にプローブを追加します。

	$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


この例では、contoso.com/path/custompath.htm という URL パスを 30 秒間隔でチェックするようにカスタム プローブを構成しています。タイムアウトのしきい値は 120 秒、プローブ要求の最大失敗回数は 8 回です。

### 手順 3.

**-Set-AzureRmApplicationGatewayBackendHttpSettings** を使用して、バックエンド プール設定の構成にプローブとタイムアウトを追加します。


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### 手順 4.

**Set-AzureRmApplicationGateway** を使用して、アプリケーション ゲートウェイに構成を保存します。

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## 既存のアプリケーション ゲートウェイからのプローブの削除

既存のアプリケーション ゲートウェイからカスタム プローブを削除する手順を次に示します。

### 手順 1

**Get-AzureRmApplicationGateway** を使用して、PowerShell 変数にアプリケーション ゲートウェイのリソースを読み込みます。

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### 手順 2.

**Remove-AzureRmApplicationGatewayProbeConfig** を使用して、アプリケーション ゲートウェイからプローブ構成を削除します。

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### 手順 3.

**-Set-AzureRmApplicationGatewayBackendHttpSettings** を使用して、バックエンド プール設定を更新してプローブとタイムアウト設定を削除します。


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### 手順 4.

**Set-AzureRmApplicationGateway** を使用して、アプリケーション ゲートウェイに構成を保存します。

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## 次のステップ

[SSL オフロードの構成](application-gateway-ssl-arm.md)に関するページを参照して、SSL オフロードを構成する方法について学習します。

<!---HONumber=AcomDC_0907_2016-->