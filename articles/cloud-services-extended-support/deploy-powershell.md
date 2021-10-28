---
title: クラウド サービス (延長サポート) をデプロイする - PowerShell
description: PowerShell を使用してクラウド サービス (延長サポート) をデプロイする
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: e157fcb3dd3c25d2724d9f0a190596549913edb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229876"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Azure PowerShell を使用してクラウド サービス (延長サポート) をデプロイする

この記事では、複数のロール (Web ロールおよび worker ロール) を備えた Cloud Services (延長サポート) を、`Az.CloudService` PowerShell モジュールを使って Azure にデプロイする方法を説明します。

## <a name="pre-requisites"></a>前提条件

1. クラウド サービス (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認し、関連するリソースを作成します。 
2. Az.CloudService PowerShell モジュールをインストールします。

    ```azurepowershell-interactive
    Install-Module -Name Az.CloudService 
    ```

3. 新しいリソース グループを作成する。 既存のリソース グループを使用する場合は、この手順は省略可能です。   

    ```azurepowershell-interactive
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

4. クラウド サービス パッケージ (.cspkg) ファイルとサービス構成 (.cscfg) ファイルの格納に使用するストレージ アカウントとコンテナーを作成します。 ストレージ アカウント名には、一意の名前を指定する必要があります。 既存のストレージ アカウントを使用する場合は、この手順は省略可能です。

    ```azurepowershell-interactive
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```
    
## <a name="deploy-a-cloud-services-extended-support"></a>クラウド サービス (延長サポート) をデプロイする

次のいずれかの PowerShell コマンドレットを使用して、Cloud Services (延長サポート) をデプロイします。

1. [**ストレージ アカウントを使用したクラウド サービスの簡易作成**](#quick-create-cloud-service-using-a-storage-account)

    - このパラメーター セットは、入力としてストレージ アカウントとともに .cscfg、.cspkg、.csdef ファイルを入力します。 
    - クラウド サービス ロール プロファイル、ネットワーク プロファイル、OS プロファイルがコマンドレットによって作成され、ユーザーからの入力は最小限に抑えられます。 
    - 証明書の入力の場合は、keyvault 名が指定されます。 keyvault 内の証明書の拇印が、.cscfg ファイルに指定されているものに対して検証されます。
    
 2. [**SAS URI を使用したクラウド サービスの簡易作成**](#quick-create-cloud-service-using-a-sas-uri)
 
    - このパラメーター セットは、.csdef および .cscfg ファイルのローカル パスとともに、.cspkg の SAS URI を入力します。 ストレージ アカウントの入力は必要ありません。 
    - クラウド サービス ロール プロファイル、ネットワーク プロファイル、OS プロファイルがコマンドレットによって作成され、ユーザーからの入力は最小限に抑えられます。 
    - 証明書の入力の場合は、keyvault 名が指定されます。 keyvault 内の証明書の拇印が、.cscfg ファイルに指定されているものに対して検証されます。
    
3. [**ロール、OS、ネットワーク、拡張機能プロファイル、SAS URI を使用してクラウド サービスを作成する**](#create-cloud-service-using-profile-objects--sas-uris)

    - このパラメーター セットは、.cscfg および .cspkg ファイルの SAS URI を入力します。
    - ロール、ネットワーク、OS、拡張機能プロファイルは、ユーザーが指定する必要があり、.cscfg と .csdef の値と一致する必要があります。 

### <a name="quick-create-cloud-service-using-a-storage-account"></a>ストレージ アカウントを使用したクラウド サービスの簡易作成

.cscfg、.csdef、.cspkg ファイルを使用してクラウド サービス デプロイを作成します。

```azurepowershell-interactive
$cspkgFilePath = "<Path to cspkg file>"
$cscfgFilePath = "<Path to cscfg file>"
$csdefFilePath = "<Path to csdef file>"
      
# Create Cloud Service       
New-AzCloudService
-Name "ContosoCS" `
-ResourceGroupName "ContosOrg" `
-Location "EastUS" `
-ConfigurationFile $cscfgFilePath `
-DefinitionFile $csdefFilePath `
-PackageFile $cspkgFilePath `
-StorageAccount $storageAccount `
[-KeyVaultName <string>]
```

### <a name="quick-create-cloud-service-using-a-sas-uri"></a>SAS URI を使用したクラウド サービスの簡易作成

1. ストレージ アカウントにクラウド サービス パッケージ (cspkg) をアップロードします。

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    $cscfgFilePath = "<Path to cscfg file>"
    $csdefFilePath = "<Path to csdef file>"
    ```

 2. .cscfg、.csdef、.cspkg SAS URI を使用してクラウド サービス デプロイを作成します。

    ```azurepowershell-interactive
    New-AzCloudService
        -Name "ContosoCS" `
        -ResourceGroupName "ContosOrg" `
        -Location "EastUS" `
        -ConfigurationFile $cspkgFilePath `
        -DefinitionFile $csdefFilePath `
        -PackageURL $cspkgUrl `
        [-KeyVaultName <string>]
    ```
    
### <a name="create-cloud-service-using-profile-objects--sas-uris"></a>プロファイル オブジェクトと SAS URI を使用してクラウド サービスを作成する

1.  ストレージ アカウントにクラウド サービス構成 (cscfg) をアップロードします。 

    ```azurepowershell-interactive
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```
2. ストレージ アカウントにクラウド サービス パッケージ (cspkg) をアップロードします。

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
    
3. 仮想ネットワークとサブネットを作成します。 既存のネットワークとサブネットを使用する場合は、この手順は省略可能です。 この例では、Web ロールと worker ロールのどちらのクラウド サービス ロールにも、単一の仮想ネットワークとサブネットを使用します。 

    ```azurepowershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
4. パブリック IP アドレスを作成し、そのパブリック IP アドレスの DNS ラベル プロパティを設定します。 Cloud Services (延長サポート) では、[Basic](../virtual-network/ip-services/public-ip-addresses.md#basic) SKU のパブリック IP アドレスのみをサポートしています。 Standard SKU のパブリック IP は、Cloud Services では機能しません。
静的 IP を使用する場合は、サービス構成 (.cscfg) ファイルで予約済み IP として参照する必要があります。

    ```azurepowershell-interactive
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

5. ネットワーク プロファイル オブジェクトを作成し、ロード バランサーのフロントエンドにパブリック IP アドレスを関連付けます。 Azure プラットフォームにより、クラウド サービス リソースと同じサブスクリプションに "クラシック" SKU ロード バランサー リソースが自動的に作成されます。 ロード バランサー リソースは、Azure Resource Manager の読み取り専用リソースです。 リソースに対する更新は、クラウド サービス デプロイ ファイル (.cscfg および .csdef) を介してのみサポートされます。

    ```azurepowershell-interactive
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
6. キー コンテナーを作成する このキー コンテナーは、クラウド サービス (延長サポート) の各種ロールに関連付けられている証明書を格納するために使用します。 このキー コンテナーは、一意の名前を使用したうえで、クラウド サービスと同じリージョンおよびサブスクリプションに配置する必要があります。 詳細については、「[Azure Cloud Services (延長サポート) で証明書を使用する](certificates-and-key-vault.md)」を参照してください。

    ```azurepowershell-interactive
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

7. キー コンテナーのアクセス ポリシーを更新し、自分のユーザー アカウントに証明書に関するアクセス許可を付与します。 

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    または、ObjectId (`Get-AzADUser` を実行すると取得できます) を使ってアクセス ポリシーを設定します。 
    
    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

8. この例では、キー コンテナーに自己署名証明書を追加します。 クラウド サービスのロールにデプロイするためには、クラウド サービス構成 (.cscfg) ファイルに証明書の拇印を追加する必要があります。 

    ```azurepowershell-interactive
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
9. OS プロファイルのメモリ内オブジェクトを作成します。 OS プロファイルは、クラウド サービスのロールに関連付ける証明書を指定するものです。 これは、前の手順で作成したのと同じ証明書になります。 

    ```azurepowershell-interactive
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

10. ロール プロファイルのメモリ内オブジェクトを作成します。 ロール プロファイルは、名前、容量、階層など、ロール SKU 固有のプロパティを定義するものです。 この例では、frontendRole と backendRole の 2 つのロールを定義しています。 ロール プロファイルの情報は、構成 (cscfg) ファイルとサービス定義 (csdef) ファイルの中で定義されているロール構成と一致している必要があります。 

    ```azurepowershell-interactive
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

11. (省略可能) クラウド サービスに追加する拡張機能プロファイルのメモリ内オブジェクトを作成します。 この例では、RDP 拡張機能を追加します。 

    ```azurepowershell-interactive
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    
    次のように、ConfigFile には PublicConfig タグのみが含まれていること、また名前空間が含まれていることが必要です。
   
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
    
12. (省略可能) クラウド サービスに追加するタグを PowerShell ハッシュ テーブルとして定義します。 

    ```azurepowershell-interactive
    $tag=@{"Owner" = "Contoso"} 
    ```

13. プロファイル オブジェクトと SAS URL を使用してクラウド サービスのデプロイを作成します。

    ```azurepowershell-interactive
    $cloudService = New-AzCloudService ` 
        -Name “ContosoCS” ` 
        -ResourceGroupName “ContosOrg” ` 
        -Location “East US” ` 
        -PackageUrl $cspkgUrl ` 
        -ConfigurationUrl $cscfgUrl ` 
        -UpgradeMode 'Auto' ` 
        -RoleProfile $roleProfile ` 
        -NetworkProfile $networkProfile  ` 
        -ExtensionProfile $extensionProfile ` 
        -OSProfile $osProfile `
        -Tag $tag 
    ```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.yml)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。