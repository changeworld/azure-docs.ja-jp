---
title: 複数のサイトをホストするアプリケーション ゲートウェイを作成する - Azure PowerShell | Microsoft Docs
description: Azure PowerShell を使用して複数のサイトをホストするアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 614a77bc21aa5f2b3b822c22f24820d617181909
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055044"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Azure PowerShell を使用して複数のサイトをホストするアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](application-gateway-introduction.md)を作成するときに、Azure PowerShell を使用して [複数の Web サイト](application-gateway-multi-site-overview.md)のホスティングを構成できます。 このチュートリアルでは、仮想マシン スケール セットを使用してバックエンド プールを作成します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するように、所有するドメインに基づいてリスナーと規則を構成します。 このチュートリアルでは、複数のドメインを所有していることを前提として、*www.contoso.com* と *www.fabrikam.com* の例を使用します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * ネットワークのセットアップ
> * アプリケーション ゲートウェイの作成
> * リスナーとルーティング規則の作成
> * バックエンド プールを含んだ仮想マシン スケール セットの作成
> * ドメインの CNAME レコードの作成

![マルチサイト ルーティングの例](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用して、*myBackendSubnet* および *myAGSubnet* という名前のサブネットを構成します。 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) とサブネット構成を使用して、*myVNet* という名前の仮想ネットワークを作成します。 最後に、[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。 こうしたリソースは、アプリケーション ゲートウェイとその関連リソースにネットワーク接続を提供するために使用されます。

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

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 構成とフロントエンド ポートの作成

[New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) を使用して、前に作成した *myAGSubnet* をアプリケーション ゲートウェイに関連付けます。 [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) を使用して、*myAGPublicIPAddress* をアプリケーション ゲートウェイに割り当てます。

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
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

### <a name="create-the-backend-pools-and-settings"></a>バックエンド プールと設定の作成

[New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) を使用して、アプリケーション ゲートウェイに対して *contosoPool* と *fabrikamPool* という名前のバックエンド プールを作成します。 [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) を使用してプールの設定を構成します。

```azurepowershell-interactive
$contosoPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$fabrikamPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>リスナーと規則の作成

アプリケーション ゲートウェイがバックエンド プールに対して適切にトラフィックをルーティングするためにはリスナーが必要です。 このチュートリアルでは、2 つのドメインのそれぞれに対してリスナーを作成します。 この例では、*www.contoso.com* と *www.fabrikam.com* のドメインに対してリスナーを作成しています。

[New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) と、前に作成したフロントエンド構成およびフロントエンド ポートを使用して、*contosoListener* と *fabrikamListener* という名前のリスナーを作成します。 着信トラフィックに使用するバックエンド プールをリスナーが判断するには、規則が必要です。 [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) を使用して、*contosoRule* と *fabrikamRule* という名前の基本規則を作成します。

```azurepowershell-interactive
$contosolistener = New-AzureRmApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"
$fabrikamlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"
$contosoRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
$fabrikamRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
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
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>仮想マシン スケール セットの作成

この例では、作成した 2 つのバックエンド プールをサポートする 2 つの仮想マシン スケール セットを作成します。 作成するスケール セットの名前は、*myvmss1* と *myvmss2* です。 各スケール セットには、IIS をインストールする 2 つの仮想マシン インスタンスが含まれています。 スケール セットは、IP 設定を構成するときにバックエンド プールに割り当てます。

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$fabrikamPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
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

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i
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

## <a name="create-cname-record-in-your-domain"></a>ドメインの CNAME レコードの作成

パブリック IP アドレスを使用してアプリケーション ゲートウェイを作成した後は、DNS アドレスを取得し、これを使用してドメインに CNAME レコードを作成できます。 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、アプリケーション ゲートウェイの DNS アドレスを取得できます。 DNSSettings の *fqdn* 値をコピーし、作成した CNAME レコードの値として使用します。 アプリケーション ゲートウェイを再起動すると VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

ブラウザーのアドレス バーにドメイン名を入力します。 http://www.contoso.com など。

![アプリケーション ゲートウェイの contoso サイトをテストする](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

アドレスをもう 1 つのドメインに変更します。次の例のように表示されます。

![アプリケーション ゲートウェイの fabrikam サイトをテストする](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * アプリケーション ゲートウェイの作成
> * リスナーとルーティング規則の作成
> * バックエンド プールを含んだ仮想マシン スケール セットの作成
> * ドメインの CNAME レコードの作成

> [!div class="nextstepaction"]
> [アプリケーション ゲートウェイでできることについてさらに学習する](application-gateway-introduction.md)
