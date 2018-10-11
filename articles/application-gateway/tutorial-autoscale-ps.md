---
title: 自動スケールの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成する - Azure PowerShell
description: Azure PowerShell を使用して、自動スケールの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成する方法について学習します。
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d86ce2e1bac2fb58df8df748381a00eac21e65cb
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016936"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して、自動スケールの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成する

このチュートリアルでは、Azure PowerShell コマンドレットと Azure Resource Manager デプロイ モデルを使用して、Azure Application Gateway を作成する方法について説明します。 このチュートリアルでは、既存の Standard SKU と比較した、新しい自動スケール SKU の相違点に重点を置いています。 具体的には、自動スケール、ゾーン冗長、および予約済み VIP (静的 IP) をサポートする機能です。

アプリケーション ゲートウェイの自動スケールとゾーン冗長の詳細については、「[自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー)](application-gateway-autoscaling-zone-redundant.md)」を参照してください。

> [!IMPORTANT]
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 自動スケールの構成パラメーターを設定する
> * ゾーン パラメーターを使用する
> * 静的 VIP を使用する
> * アプリケーション ゲートウェイの作成


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルでは、Azure PowerShell をローカルで実行する必要があります。 Azure PowerShell モジュール バージョン 6.9.0 以降がインストールされている必要があります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzureRmAccount` を実行して Azure との接続を作成します。

## <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```
## <a name="create-a-resource-group"></a>リソース グループの作成
利用可能ないずれかの場所で、リソース グループを作成します。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-vnet"></a>VNet を作成する
自動スケール アプリケーション ゲートウェイ用の 1 つの専用サブネットを持つ VNet を作成します。 現在は、専用サブネットごとに 1 つの自動スケール アプリケーション ゲートウェイしかデプロイできません。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>予約済みパブリック IP を作成する

PublicIPAddress の割り当て方法を **Static** として指定します。 自動スケール アプリケーション ゲートウェイ VIP は、静的のみです。 動的 IP はサポートされていません。 標準の PublicIpAddress SKU のみがサポートされています。

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>詳細を取得する

ローカル オブジェクトのリソース グループ、サブネット、および IP の詳細を取得して、アプリケーション ゲートウェイ IP の構成の詳細を作成します。

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>アプリケーション ゲートウェイ インフラストラクチャを構成する
既存の Standard Application Gateway と同じ形式で、IP 構成、フロントエンド IP 構成、バックエンド プール、HTTP 設定、証明書、ポート、リスナー、およびルールを構成します。 新しい SKU は、Standard SKU と同じオブジェクト モデルに従います。

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>自動スケールを指定する

これで、アプリケーション ゲートウェイに自動スケールの構成を指定できます。 次の 2 種類の自動スケール構成がサポートされています。

- **固定容量モード**。 このモードでは、アプリケーション ゲートウェイは自動スケールせず、固定されたスケール ユニットの容量で動作します。

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```
- **自動スケール モード**。 このモードでは、アプリケーション ゲートウェイは、アプリケーションのトラフィック パターンに基づいて、自動スケールします。

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

Application Gateway を作成して、冗長ゾーンを含めます。 

ゾーン構成は、Azure ゾーンが使用可能なリージョンでのみサポートされます。 Azure ゾーンが使用できないリージョンでは、ゾーン パラメーターを使用しないでください。 アプリケーション ゲートウェイは、1 つのゾーン、2 つのゾーン、または 3 つのゾーンすべてにデプロイすることもできます。 1 つのゾーンのアプリケーション ゲートウェイの PublicIPAddress は、同じゾーンにバインドする必要があります。 2 つまたは 3 つのゾーン冗長アプリケーション ゲートウェイの PublicIPAddress は、どのゾーンも指定されないように、ゾーン冗長にする必要もあります。

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスは、[Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して取得します。 パブリック IP アドレスまたは DNS 名をコピーして、それをお使いのブラウザーのアドレス バーに貼り付けます。

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>リソースのクリーンアップ
まずアプリケーション ゲートウェイによって作成されたリソースを調べ、必要がなくなったら、`Remove-AzureRmResourceGroup` コマンドを使用して、リソース グループ、アプリケーションゲートウェイ、およびすべての関連リソースを削除できます。

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 静的 VIP を使用する
> * 自動スケールの構成パラメーターを設定する
> * ゾーン パラメーターを使用する
> * アプリケーション ゲートウェイの作成

> [!div class="nextstepaction"]
> [URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](./tutorial-url-route-powershell.md)
