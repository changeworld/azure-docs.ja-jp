---
title: URL に基づいて Web トラフィックをルーティングする - Azure PowerShell
description: Azure PowerShell を使用して、URL に基づいて Web トラフィックをサーバーの特定のスケーラブルなプールにルーティングする方法を説明します。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/13/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 66f79b68c003aa3605653b0decc091d22fbf3860
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055187"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Azure PowerShell を使用して、URL に基づいて Web トラフィックをルーティングする

Azure PowerShell を使用して、アプリケーションにアクセスするために使用する URL に基づいた特定のスケーラブルなサーバー プールへの Web トラフィックのルーティングを構成できます。 このチュートリアルでは、[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用して、3 つのバックエンド プールがある [Azure Application Gateway ](application-gateway-introduction.md)を作成します。 各バックエンド プールは、共通のデータ、画像、およびビデオなどの特定の目的に役立ちます。  個別のプールにトラフィックをルーティングすることにより、ユーザーが必要なときに必要な情報を取得できます。

トラフィックのルーティングを有効にするには、特定のポートでリッスンするリスナーに割り当てられる[ルーティング規則](application-gateway-url-route-overview.md)を作成し、Web トラフィックが、プール内の適切なサーバーに到着するようにします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * リスナー、URL パス マップ、およびルールの作成
> * スケーラブルなバックエンド プールの作成

![URL ルーティングの例](./media/tutorial-url-route-powershell/scenario.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

リソースの作成に時間が必要なため、このチュートリアルを完了するのに最大で 90 分かかる場合があります。

## <a name="create-a-resource-group"></a>リソース グループの作成

アプリケーションのすべてのリソースを含むリソース グループを作成する必要があります。 

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

既存の仮想ネットワークがある場合も、新しく作成する場合も、そのネットワークに、アプリケーション ゲートウェイのみに使用するサブネットが含まれるようにする必要があります。 このチュートリアルでは、アプリケーション ゲートウェイのサブネットと、スケール セットのサブネットを作成します。 パブリック IP アドレスを作成し、アプリケーション ゲートウェイ内のリソースにアクセスできるようにします。

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用して、サブネット構成 *myAGSubnet* および *myBackendSubnet* を作成します。 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) とサブネット構成を使用して、*myVNet* という名前の仮想ネットワークを作成します。 最後に、[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。 こうしたリソースは、アプリケーション ゲートウェイとその関連リソースにネットワーク接続を提供するために使用されます。

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

このセクションでは、アプリケーション ゲートウェイをサポートするリソースを作成し、最終的にアプリケーション ゲートウェイを作成します。 作成するリソースは次のとおりです。

- *IP 構成とフロントエンド ポート* - 以前に作成したサブネットをアプリケーション ゲートウェイに関連付け、それへのアクセスに使用するポートを割り当てます。
- *既定のプール* - すべてのアプリケーション ゲートウェイには、サーバーのバックエンド プールが 1 つ以上必要です。
- *既定のリスナーとルール* - 既定のリスナーは、割り当てられたポートでトラフィックをリッスンし、既定のルールは、既定のプールにトラフィックを送信します。

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 構成とフロントエンド ポートの作成

[New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) を使用して、前に作成した *myAGSubnet* をアプリケーション ゲートウェイに関連付けます。 [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) を使用して、*myAGPublicIPAddress* をアプリケーション ゲートウェイに割り当てます。

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>既定のプールと設定の作成

[New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) を使用して、アプリケーション ゲートウェイに対して *appGatewayBackendPool* という名前の既定のバックエンド プールを作成します。 [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) を使用して、バックエンド プールの設定を構成します。

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>既定のリスナーとルールの作成

アプリケーション ゲートウェイがバックエンド プールへのトラフィックを適切にルーティングできるようにするにはリスナーが必要です。 このチュートリアルでは、2 つのリスナーを作成します。 最初に作成する基本的なリスナーは、ルート URL でトラフィックをリッスンします。 2 番目に作成するリスナーは、特定の URL でトラフィックをリッスンします。

[New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) と、前に作成したフロントエンド構成およびフロントエンド ポートを使用して、*myDefaultListener* という名前の既定のリスナーを作成します。 

着信トラフィックに使用するバックエンド プールをリスナーが判断するには、ルールが必要です。 [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) を使用して、*rule1* という名前の基本ルールを作成します。

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

必要な関連リソースを作成したら、[New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) を使用して *myAppGateway* という名前のアプリケーション ゲートウェイのパラメーターを指定し、[New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) を使用してそれを作成します。

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

アプリケーション ゲートウェイの作成には最大 30 分かかる場合があります。デプロイが正常に終了するのを待ってから、次のセクションに進みます。 チュートリアルのこの時点で、ポート 80 でトラフィックをリッスンしていて、サーバーの既定のプールにそのトラフィックを送信するアプリケーション ゲートウェイがあります。

### <a name="add-image-and-video-backend-pools-and-port"></a>イメージおよびビデオのバックエンド プールとポートの追加

*imagesBackendPool* および *videoBackendPool* という名前のバックエンド プールを、アプリケーション ゲートウェイ [Add-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool) に追加します。 [Add-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport) を使用して、プールのフロントエンド ポートを追加します。 [Set-AzureRmApplicationGateway](/powershell/module/azurerm.network/set-azurermapplicationgateway) を使用して、アプリケーション ゲートウェイに変更を送信します。

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

アプリケーション ゲートウェイの更新にも、完了まで最大 20 分かかることがあります。

### <a name="add-backend-listener"></a>バックエンド リスナーの追加

[Add-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener) を使用して、トラフィックのルーティングに必要な *backendListener* という名前のバックエンド リスナーを追加します。

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>URL パス マップの追加

URL パス マップにより、アプリケーションに送信される URL が特定のバックエンド プールに確実にルーティングされます。 [New-AzureRmApplicationGatewayPathRuleConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewaypathruleconfig) および [Add-AzureRmApplicationGatewayUrlPathMapConfig](/powershell/module/azurerm.network/add-azurermapplicationgatewayurlpathmapconfig) を使用して、*imagePathRule* および *videoPathRule* という名前の URL パス マップを作成します。

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>ルーティング規則の追加

ルーティング規則は、URL マップを、作成したリスナーに関連付けます。 [Add-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule) を使用して、*rule2* という名前のルールを追加します。

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>仮想マシン スケール セットの作成

この例では、作成した 3 つのバックエンド プールをサポートする 3 つの仮想マシン スケール セットを作成します。 作成するスケール セットの名前は、*myvmss1*、*myvmss2*、および *myvmss3* です。 スケール セットは、IP 設定を構成するときにバックエンド プールに割り当てます。

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzureRmVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzureRmVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzureRmVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>IIS のインストール

各スケール セットには、IIS をインストールする仮想マシン インスタンスが 2 つ含まれていて、この IIS で、アプリケーション ゲートウェイが動作しているかどうかをテストするサンプル ページを実行します。

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスは、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して取得します。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 たとえば、*http://52.168.55.24*、*http://52.168.55.24:8080/images/test.htm*、*http://52.168.55.24:8080/video/test.htm* などです。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![アプリケーション ゲートウェイでのベース URL のテスト](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

URL を http://&lt;ip-address&gt;:8080/images/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

URL を http://&lt;ip-address&gt;:8080/video/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * リスナー、URL パス マップ、およびルールの作成
> * スケーラブルなバックエンド プールの作成

> [!div class="nextstepaction"]
> [URL に基づく Web トラフィックのリダイレクト](./tutorial-url-redirect-powershell.md)
