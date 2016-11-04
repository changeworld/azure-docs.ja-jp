---
title: Azure リソース マネージャーを使用した内部ロード バランサー (ILB) での Application Gateway の作成 | Microsoft Docs
description: このページでは、Azure リソース マネージャーで内部ロード バランサー (ILB) を使用して、Azure Application Gateway を作成、構成、起動、および削除する方法について説明します。
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/19/2016
ms.author: gwallace

---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-(ilb)-by-using-azure-resource-manager"></a>Azure リソース マネージャーを使用した内部ロード バランサー (ILB) での Application Gateway の作成
> [!div class="op_single_selector"]
> * [Azure クラシックの手順](application-gateway-ilb.md)
> * [Resource Manager の PowerShell の手順](application-gateway-ilb-arm.md)
> 
> 

Azure Application Gateway は、インターネットに接続する VIP のほか、内部ロード バランサー (ILB) エンドポイントとも呼ばれるインターネットに接続されていない内部エンドポイントを使用して構成できます。 ILB を使用したゲートウェイの構成は、インターネットに接続されていない社内用ビジネス アプリケーションで便利です。 また、セキュリティの境界でインターネットに接続されていない多階層アプリケーション内のサービスや階層でも便利ですが、ラウンド ロビンの負荷分散、セッションの持続性、または Secure Sockets Layer (SSL) ターミネーションが必要です。

この記事では、ILB を使用してアプリケーション ゲートウェイを構成する手順について説明します。

## <a name="before-you-begin"></a>開始する前に
1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. Application Gateway の仮想ネットワークとサブネットを作成します。 仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。 Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。つまり、仮想ネットワーク内にエンドポイントが作成されているか、割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## <a name="what-is-required-to-create-an-application-gateway?"></a>Application Gateway の作成に必要な構成
* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのアプリケーション ゲートウェイ用の別のサブネットに属しているか、パブリック IP/VIP である必要があります。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。 現在、 *basic* ルールのみサポートされます。 *basic* ルールは、ラウンド ロビンの負荷分散です。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成
Azure クラシックと Azure Resource Manager の使用方法の違いは、設定が必要なアプリケーション ゲートウェイと項目を作成する順番にあります。
Resource Manager を使用すると、アプリケーション ゲートウェイを作成するすべての項目は個別に構成され、その後結合されてアプリケーション ゲートウェイのリソースが作成されます。

Application Gateway を作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループの作成
2. アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。
3. Application Gateway 構成オブジェクトの作成
4. アプリケーション ゲートウェイのリソースの作成

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成
Azure リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。 詳細については、「 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。

### <a name="step-1"></a>手順 1
    Login-AzureRmAccount

### <a name="step-2"></a>手順 2.
アカウントのサブスクリプションを確認します。

    Get-AzureRmSubscription

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### <a name="step-3"></a>手順 3.
使用する Azure サブスクリプションを選択します。 <BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>手順 4.
新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-rg" という名前のリソース グループと "West US" という名前の場所を作成しました。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。
次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### <a name="step-1"></a>手順 1
    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

### <a name="step-2"></a>手順 2.
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ "appgw-rg" に、"appgwvnet" という名前の仮想ネットワークを作成します。

### <a name="step-3"></a>手順 3.
    $subnet = $vnet.subnets[0]

変数 $subnet にサブネット オブジェクトを割り当てます。

## <a name="create-an-application-gateway-configuration-object"></a>Application Gateway 構成オブジェクトの作成
### <a name="step-1"></a>手順 1
    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

"gatewayIP01" という名前の Application Gateway の IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

### <a name="step-2"></a>手順 2.
    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

IP アドレス "134.170.185.46, 134.170.188.221,134.170.185.50" を使用して、"pool01" という名前のバックエンド IP アドレス プールを構成します。 これらは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換えます。

### <a name="step-3"></a>手順 3.
    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、Application Gateway の設定 "poolsetting01" を構成します。

### <a name="step-4"></a>手順 4.
    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

ILB に対して、"frontendport01" という名前のフロントエンド IP ポートを構成します。

### <a name="step-5"></a>手順 5.
    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

"fipconfig01" というフロントエンド IP 構成を作成し、現在の仮想ネットワーク サブネットからプライベート IP を関連付けます。

### <a name="step-6"></a>手順 6.
    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

"listener01" というリスナーを作成し、フロントエンド IP 構成にフロントエンド ポートを関連付けます。

### <a name="step-7"></a>手順 7.
    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

"rule01" というロード バランサーのルーティング規則を作成し、ロード バランサーの動作を構成します。

### <a name="step-8"></a>手順 8.
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Application Gateway のインスタンスのサイズを構成します。

> [!NOTE]
> *InstanceCount* の既定値は 2、最大値は 10 です。 *GatewaySize* の既定値は Medium です。 Standard_Small、Standard_Medium、Standard_Large のいずれかを選択できます。
> 
> 

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>New-AzureApplicationGateway を使用した Application Gateway の作成
前の手順の構成項目をすべて使用して、Application Gateway を作成します。 この例では、アプリケーション ゲートウェイは "appgwtest" という名前です。

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

前の手順の構成項目をすべて使用して、Application Gateway を作成します。 この例では、Application Gateway は "appgwtest" という名前です。

## <a name="delete-an-application-gateway"></a>Application Gateway の削除
Application Gateway を削除するには、次の手順を順番に実行する必要があります。

1. **Stop-AzureRmApplicationGateway** コマンドレットを使用して、ゲートウェイを停止します。
2. **Remove-AzureRmApplicationGateway** コマンドレットを使用して、ゲートウェイを削除します。
3. **Get-AzureApplicationGateway** コマンドレットを使用して、ゲートウェイが削除されたことを確認します。

### <a name="step-1"></a>手順 1
Application Gateway オブジェクトを取得し、変数 "$getgw" に関連付けます。

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>手順 2.
**Stop-AzureRmApplicationGateway** を使用して、アプリケーション ゲートウェイを停止します。 次の例では、最初の行に **Stop-AzureRmApplicationGateway** コマンドレットを示し、続いてその出力を示します。

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

アプリケーション ゲートウェイが停止状態になったら、 **Remove-AzureRmApplicationGateway** コマンドレットを使用してサービスを削除します。

    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

> [!NOTE]
> **-force** スイッチを使用すると、削除の確認メッセージを表示しないように設定できます。
> 
> 

サービスが削除されたことを確認するには、 **Get-AzureRmApplicationGateway** コマンドレットを使用します。 この手順は必須ではありません。

    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>次のステップ
SSL オフロードを構成する場合は、「 [クラシック デプロイ モデルを使用して SSL オフロード用にアプリケーション ゲートウェイを構成する](application-gateway-ssl.md)」を参照してください。

ILB と共に使用するようにアプリケーション ゲートウェイを構成する場合は、「 [内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Oct16_HO2-->


