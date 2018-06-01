---
title: ゾーン冗長 Azure アプリケーション ゲートウェイを作成する
description: ゾーンにごとに個別にゲートウェイを必要としない、ゾーン冗長アプリケーション ゲートウェイを作成する方法を説明します。
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937705"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>ゾーン冗長 Azure アプリケーション ゲートウェイを作成する - プライベート プレビュー

アプリケーション ゲートウェイ ゾーン冗長プラットフォームでは、既存のアプリケーション ゲートウェイ SKU に対して、次のような多くの拡張機能を提供する新しい SKU です。
- **ゾーン回復性** - アプリケーション ゲートウェイの展開が複数の可用性ゾーンを対象にできるようになり、トラフィック マネージャーを使ってゾーンごとに個別にアプリケーション ゲートウェイのインスタンスをプロビジョニングして固定する必要がなくなります。 アプリケーション ゲートウェイのインスタンスを展開する 1 つまたは複数のゾーンを選択でき、ゾーンの障害の回復性が保証されます。 アプリケーションのバックエンド プールも、複数の可用性ゾーンに同様に分散できます。
- **パフォーマンスの強化**
- **静的 VIP** - アプリケーション ゲートウェイの VIP は、静的な VIP の種類だけをサポートするようになります。 これにより、アプリケーション ゲートウェイに関連付けられた VIP は、再起動後に変化しません。
- **顧客 SSL 証明書に対するキー コンテナーの統合**
- **展開と更新の時間の短縮**

> [!NOTE]
> 現在、ゾーン冗長アプリケーション ゲートウェイはプライベート プレビューです。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="supported-regions"></a>サポートされているリージョン

現在、ゾーン冗長アプリケーション ゲートウェイは米国東部 2 リージョンでサポートされています。 近日中に、さらにリージョンが追加される予定です。

## <a name="topologies"></a>トポロジ

現在のリリースでは、ゾーンの冗長性を確保するため、ゾーン固定のアプリケーション ゲートウェイを作成する必要がなくなります。 同じアプリケーション ゲートウェイの展開を、複数のゾーンに広げることができます。

3 つゾーンすべてを対象にするには、少なくとも 3 つのインスタンスが必要です。 アプリケーション ゲートウェイは、インスタンスが追加されると、ゾーン間にインスタンスを分散させます。

以前のゾーン冗長トポロジは、次の図のようなものでした。

![古い冗長トポロジ](media/create-zone-redundant/old-redundant.png)

新しいゾーン冗長トポロジは、次の図のようになります。

![新しいゾーン冗長トポロジ](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>デプロイ

### <a name="prerequisites"></a>前提条件

現在、ゾーン冗長機能はプライベート プレビューでのみ使用できます。 ホワイトリストに登録するには、appgwxzone@microsoft.com にメールを送る必要があります。 確認を受け取った後、次の手順に進むことができます。 ホワイトリスト登録のメールには、次の情報を含めてください。

- サブスクリプション ID
- リージョン名
- 必要なアプリケーション ゲートウェイの概数

### <a name="resource-manager-template-deployment"></a>Resource Manager テンプレートの展開

1. 前に説明したように、使用するサブスクリプションがホワイトリストに登録されていることを確認します。
2. 確認を受け取った後、Azure アカウントにサインインし、複数のサブスクリプションが存在する場合は、適切なサブスクリプションを選びます。 ホワイトリストに登録されたサブスクリプションを選択してください。

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. 新しいリソース グループを作成する

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. [GitHub](https://github.com/amitsriva/CrossZonePreview) からテンプレートをダウンロードし、保存したフォルダーをメモしておきます。
5. 作成したリソース グループに新しい展開を作成します。 展開する前に、テンプレートとパラメーター ファイルを適切に変更します。 

   次の図では、Azure portal でテナント ID を取得できる場所を示します。

   ![ポータルからのテナント ID](media/create-zone-redundant/tenant-id.png)

このテンプレートは、次のリソースを作成します。

- **ユーザー割り当て ID** - これは、アプリケーション ゲートウェイのインスタンスがキー コンテナーにアクセスし、ユーザーが格納した証明書を取得できるようにするために使われます。
- **KeyVault** – ユーザーの証明書が格納されるキー コンテナーです。 既存のキー コンテナーでもかまいません。
- **シークレット** – キー コンテナーに格納される秘密キーです。
- **アクセス ポリシー** – キー コンテナーに適用されるアクセス ポリシーであり、アプリケーション ゲートウェイの展開がユーザー証明書を取得できるように、ユーザー割り当て ID を使用するアクセス許可を付与します。
- **パブリック IP アドレス** – アプリケーション ゲートウェイにアクセスするために使われる予約済み IP アドレスです。 アプリケーション ゲートウェイのライフサイクルを通じて、この IP アドレスは変化しません。
- **ネットワーク セキュリティ グループ** – 構成されたリスナーにおいてポート トラフィックを開くアプリケーション ゲートウェイ サブネット上に自動的に作成される NSG です。 以前の SKU ではこの NSG は暗黙的でしたが、新しい SKU では明示的に作成されて管理されます。
- **仮想ネットワーク** – アプリケーション ゲートウェイとアプリケーションが展開される仮想ネットワークです。
- **アプリケーション ゲートウェイ** – 必要なゾーンのインスタンスを使ってアプリケーション ゲートウェイを作成します。 既定では、すべてのゾーン (1、2、3) が選択されています。 SKU の名前は、*Standard_v2* に変更されます。 この SKU の名前は変更される可能性があります。 現在、自動スケーリングの構成では、最小と最大が必要なインスタンス数に設定されています。 自動スケーリングを有効にすると、これを調整することができます。

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell でのデプロイ

1. 前提条件で前述したように、使用するサブスクリプションがホワイトリストに登録されていることを確認します。
2. プライベート PowerShell MSI を [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi) からダウンロードしてインストールします。
3. プレビュー登録確認メールに記載されている場所からプライベート PowerShell zip ファイルをダウンロードします。 ドライブにファイルを解凍し、場所をメモしておきます。
4. プレビューが有効になった後、アカウントにサインオンする前にまず、プレビュー モジュールを読み込みます。

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Azure アカウントにサインインし、複数のサブスクリプションが存在する場合は、適切なサブスクリプションを選びます。 ホワイトリストに登録された適切なサブスクリプションを選択してください。
5. 次のコマンドを実行し、作成されるほとんどのエンティティの名前を含む共通定数を設定します。 

   お使いの名前付け設定の必要に応じて、エントリを変更します。

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. リソース グループを作成します。

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. キー コンテナーから証明書を取得するためにアプリケーション ゲートウェイにアクセスできるようにするために使用される、ユーザー割り当て ID を作成します。

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. 証明書の格納に使われるキー コンテナーを作成します。

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. 証明書をシークレットとしてキー コンテナーにアップロードします

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. ユーザー割り当て ID を使って、キー コンテナーへのアクセス ポリシーを割り当てます。 これにより、アプリケーション ゲートウェイのインスタンスはキー コンテナーのシークレットにアクセスできます。

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. 新しいリスナーが作成されるポートでアプリケーション ゲートウェイ サブネットにアクセスできるようにするための、ネットワーク セキュリティ グループ (NSG) を作成します。

    たとえば、既定のポート上の HTTP/HTTPS では、NSG は管理操作のために 80、443、65200-65535 への受信アクセスを許可します。

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. VNet とサブネットを作成します。

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. 予約済み/静的の種類でパブリック IP アドレスを作成します。

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. アプリケーション ゲートウェイを作成します。

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. 作成されたアプリケーション ゲートウェイのパブリック IP アドレスを取得します。

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

-  プレビューのアプリケーション ゲートウェイは課金されますか。

   プレビューの期間中は、料金はかかりません。 キー コンテナーや仮想マシンなど、アプリケーション ゲートウェイ以外のリソースに対しては課金されます。
- このプレビューは、どのリージョンで利用できますか。

   現在、プレビューは米国東部 2 リージョンで使用できます。 近日中に、さらにリージョンが追加される予定です。
- ポータルはプレビューでサポートされますか。

   いいえ。プライベート プレビュー中のサポートは、プライベート PowerShell モジュールと Resource Manager テンプレートに制限されます。

- プライベート プレビュー期間中は、運用ワークロードはサポートされますか。

   いいえ。プライベート プレビュー中は SLA またはサポートはありません。 プレビュー期間中は、運用ワークロードを配置することは推奨されません。 プレビューのサポートは、メール エイリアスによる製品グループとの直接的なやり取りに制限されます。

- 問題はどのようにレポートすればよいですか。

   プライベート プレビューでは、バグが含まれ、頻繁にコードが展開される可能性があります。 問題の報告と支援の要求には、サポートの別名 appgwxzone@microsoft.com を使ってください。

## <a name="known-issues-and-limitations"></a>既知の問題と制限


|問題  |詳細  |
|---------|---------|---------|
|課金     |現在は請求されません|
|診断ログ (メトリックなし)     |パフォーマンスと要求/応答のログは、現在表示されません。|
|ポータル/CLI/SDK     |ポータル、CLI、または SDK はサポートされません。 プレビュー ゲートウェイへの更新プログラムの発行に、ポータルを使わないでください。|
|テンプレートによる更新は失敗することがあります     |これは KeyVault アクセス ポリシーとの競合状態のためです。|キー コンテナーとユーザー割り当て ID が作成されたら、テンプレートから削除できます。テンプレートではシークレットと ID への参照のみが必要です。|
|自動スケール     |現在、自動スケーリングはサポートされていません|
|WAF     |現在 WAF はサポートされていません|
|ユーザー提供証明書と動的 VIP     |新しいモデルではサポートされていません。 証明書と静的 VIP の格納には、キー コンテナーを使います。|
|アプリケーション ゲートウェイの旧バージョンとプレビュー バージョンに対する同じサブネット     |既存のアプリケーション ゲートウェイ (古いモデル) が含まれるサブネットは、プライベート プレビュー バージョンには使用できません。|
|HTTP/2、FIPS モード、WebSocket、バックエンドとしての Azure Web Apps     |現在はサポートされていません |


## <a name="support-and-feedback"></a>サポートとフィードバック

サポートおよびフィードバックについては、appgwxzone@microsoft.com にお問い合わせください。アプリケーション ゲートウェイの製品グループは、機能の向上に関するご意見をお待ちしています。必要に応じて、ガイダンスを提供します。

## <a name="next-steps"></a>次の手順

アプリケーション ゲートウェイの他の機能について学習する

- [Azure Application Gateway とは](overview.md)