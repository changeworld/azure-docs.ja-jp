---
title: クイック スタート - Azure Application Gateway による Web トラフィックのルーティング - PowerShell | Microsoft Docs
description: Azure PowerShell を使用して、Web トラフィックをバックエンド プール内の仮想マシンにルーティングする Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cb5a6a21cd6d33316e0560d7641bee99b2102373
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159827"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>クイック スタート:Azure Application Gateway による Web トラフィックの転送 - Azure PowerShell

このクイック スタートでは、Azure portal を使用して、そのバックエンド プール内の 2 つの仮想マシンで、アプリケーション ゲートウェイをすばやく作成する方法を示します。 さらに、それをテストし、正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="run-azure-powershell-locally"></a>ローカルでの Azure PowerShell の実行

Azure PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 

1. バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 
2. Azure との接続を作成するには、`Login-AzureRmAccount` を実行します。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 次のように [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドレットを使用して、リソース グループを作成します。 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

アプリケーション ゲートウェイが他のリソースと通信できるように、仮想ネットワークを作成します。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。

1. [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を呼び出してサブネット構成を作成します。
2. [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を呼び出して、サブネット構成と共に仮想ネットワークを作成します。
3. [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を呼び出して、パブリック IP アドレスを作成します。

```azurepowershell-interactive
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、Azure によってアプリケーション ゲートウェイのバックエンド サーバーとして使用される 2 つの仮想マシンを作成します。 また、仮想マシンに IIS をインストールして、Azure によってアプリケーション ゲートウェイが正常に作成されたことを確認します。

### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

1. [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用してネットワーク インターフェイスを作成します。 
2. [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して仮想マシンの構成を作成します。
3. [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。

次のコード サンプルを実行して仮想マシンを作成するときに、Azure から資格情報の入力を求められます。 ユーザー名として「*azureuser*」を、パスワードとして「*Azure123456!*」を 入力します。
    
```azurepowershell-interactive
$vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork myVNet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzureRmNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzureRmVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzureRmVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzureRmVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzureRmVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 構成とフロントエンド ポートの作成

1. [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) を使用して、お客様が作成したサブネットをアプリケーション ゲートウェイに関連付けるための構成を作成します。 
2. [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) を使用して、お客様が先ほど作成したパブリック IP アドレスをアプリケーション ゲートウェイに割り当てるための構成を作成します。 
3. [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) を使用して、アプリケーション ゲートウェイにアクセスするためのポート 80 を割り当てます。

```azurepowershell-interactive
$vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork myVNet -Name myAGSubnet
$pip    = Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>バックエンド プールの作成

1. アプリケーション ゲートウェイのバックエンド プールは、[New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) を使用して作成します。 
2. [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) を使用して、バックエンド プールの設定を構成します。

```azurepowershell-interactive
$address1 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>リスナーの作成とルールの追加

Azure では、アプリケーション ゲートウェイを有効にしてトラフィックをバックエンド プールに適切にルーティングするためにリスナーが必要です。 また、受信トラフィックに使用するバックエンド プールをリスナーが判断するには、ルールが必要です。 

1. 先ほど作成したフロントエンド構成とフロントエンド ポートを指定して [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) を使用し、リスナーを作成します。 
2. [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) を使用して、*rule1* という名前のルールを作成します。 

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

必要な関連リソースを作成したところで、アプリケーション ゲートウェイを作成します。

1. [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) を使用して、アプリケーション ゲートウェイのパラメーターを指定します。
2. [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) を使用して、アプリケーション ゲートウェイを作成します。

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

IIS はアプリケーション ゲートウェイを作成するのに必要ではありませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するためにインストールしました。 IIS を使用してアプリケーション ゲートウェイをテストします。

1. [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を実行して、アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 ブラウザーを更新したら、仮想マシンの名前が表示されるはずです。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![アプリケーション ゲートウェイのテスト](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。 

リソース グループを削除するには、次のように [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して、アプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-powershell.md)

