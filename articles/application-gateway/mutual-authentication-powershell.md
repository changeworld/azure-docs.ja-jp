---
title: PowerShell を使用して Azure Application Gateway での相互認証を構成する
description: PowerShell を使用して、相互認証を行うようにアプリケーション ゲートウェイを構成する方法について説明します
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230940"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>PowerShell を使用して Application Gateway での相互認証を構成する (プレビュー)
この記事では、PowerShell を使用して、アプリケーション ゲートウェイで相互認証を構成する方法について説明します。 相互認証とは、Application Gateway にアップロードされるクライアント証明書を使用して、要求を送信するクライアントを Application Gateway が認証することを意味します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事では、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="before-you-begin"></a>始める前に

アプリケーション ゲートウェイでの相互認証を構成するには、ゲートウェイにアップロードするクライアント証明書が必要です。 クライアント証明書は、クライアントが Application Gateway に提示する証明書の検証に使用されます。 テスト目的で、自己署名証明書を使用してもかまいません。 ただし、これらは管理が難しく、完全にセキュリティで保護されていないため、運用環境のワークロードには推奨されません。

アップロードできるクライアント証明書の種類などの詳細については、[Application Gateway での相互認証の概要](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)に関するセクションを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

まず、サブスクリプションに新しいリソース グループを作成します。 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

アプリケーション ゲートウェイのデプロイ先となる仮想ネットワークをデプロイします。

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>パブリック IP を作成します。

アプリケーション ゲートウェイで使用するパブリック IP を作成します。 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Application Gateway の IP 構成を作成する

IP 構成とフロントエンド ポートを作成します。 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>フロントエンド SSL を構成する 

アプリケーション ゲートウェイの SSL 証明書を構成します。

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>クライアント認証の構成 

アプリケーション ゲートウェイでクライアント認証を構成します。 ここで使用する信頼されたクライアント CA 証明書チェーンを抽出する方法の詳細については、[信頼されたクライアント CA 証明書チェーンを抽出する方法](./mutual-authentication-certificate-management.md)に関するページを参照してください。

> [!IMPORTANT]
> クライアント CA 証明書チェーン全体を 1 つのファイルでアップロードし、ファイルにはチェーンが 1 つだけ含まれるようにしてください。  

> [!NOTE]
> 今後は TLS 1.2 が必須となるため、相互認証には TLS 1.2 を使用することをお勧めします。 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>バックエンド プールと設定を構成する

アプリケーション ゲートウェイのバックエンド プールと設定をセットアップします。 必要に応じて、エンド ツー エンド SSL 暗号化用に、バックエンドの信頼されたルート証明書を設定します。  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>ルールを構成する

アプリケーション ゲートウェイにルールを設定します。

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>今後のリスナーに対して既定の SSL ポリシーを設定する

相互認証を設定するときに、リスナー固有の SSL ポリシーを設定しました。 この手順では、必要に応じて、今後作成するリスナーのための既定の SSL ポリシーを設定できます。 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

上で作成したすべてのものを使用して、アプリケーション ゲートウェイをデプロイします。

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>期限切れのクライアント CA 証明書を更新する

クライアント CA 証明書の有効期限が切れている場合は、次の手順に従って、ゲートウェイの証明書を更新できます。 

1. Azure へのサインイン
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. アプリケーション ゲートウェイの構成を取得します
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. 信頼されたクライアント証明書をゲートウェイから削除します 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. 新しい証明書をゲートウェイに追加します 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. 新しい証明書を使用してゲートウェイを更新します 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)