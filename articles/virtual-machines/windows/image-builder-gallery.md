---
title: Windows VM のギャラリーで Azure Image Builder を使用する
description: Azure Image Builder と Azure PowerShell を使用し、Azure Shared Gallery イメージ バージョンを作成します。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 224a24de18060568ef8b5ba86f9da8354fab8ddd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462881"
---
# <a name="create-a-windows-image-and-distribute-it-to-an-azure-compute-gallery"></a>Windows イメージを作成して Azure Compute Gallery に配布する 

**適用対象:** :heavy_check_mark: Windows VM 

この記事では、Azure Image Builder と Azure PowerShell を使用して [Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) でイメージ バージョンを作成し、そのイメージをグローバルに配布する方法について説明します。 この操作は [Azure CLI](../linux/image-builder-gallery.md) で実行することもできます。

.json テンプレートを使って、イメージを構成します。 今回使用する .json ファイルは [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json) です。 テンプレートのローカル バージョンをダウンロードして編集します。そのため、この記事はローカル PowerShell セッションを使用して作成されています。

イメージを Azure Compute Gallery に配布するため、テンプレートでは `distribute` セクションの値として [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) が使われています。

Azure Image Builder では、sysprep が自動的に実行され、イメージが汎用化されます。これは汎用 sysprep コマンドであり、必要に応じて[オーバーライド](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully)できます。 

カスタマイズを層にする回数にご注意ください。 1 つの Windows イメージで Sysprep コマンドを実行できる回数には制限があります。 Sysprep の制限に達したら、Windows イメージを作成し直す必要があります。 詳細は、[Sysprep の実行回数制限](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)に関するページを参照してください。 


## <a name="register-the-features"></a>機能の登録
Azure Image Builder を使用するには、機能を登録する必要があります。

プロバイダー登録を確認してください。 いずれからも `Registered` が返されることを確認します。

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

`Registered` が返されない場合、次を使用してプロバイダーを登録します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

PowerShell モジュールをインストールします。
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>変数の作成

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。 `username` や `vmpassword` などの変数の値は、ご自分の情報に置き換えてください。

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Azure Compute Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>ユーザー割り当て ID を作成し、リソース グループにアクセス許可を設定する
Image Builder は、指定された[ユーザー ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) を使用して、Azure Compute Gallery (SIG) にイメージを挿入します。 この例では、イメージの SIG への配布を実行するための粒度の細かいアクションを持つ Azure ロール定義を作成します。 このロール定義はその後、ユーザー ID に割り当てられます。

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>イメージを配布するためにアクセス許可を ID に割り当てる

このコマンドは、Azure のロールの定義テンプレートをダウンロードし、前に指定したパラメーターを使用してテンプレートを更新します。

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-azure-compute-gallery"></a>Azure Compute Gallery を作成する

Azure Compute Gallery で Image Builder を使用するには、既存のギャラリーとイメージ定義が必要です。 Image Builder では、ギャラリーとイメージ定義は自動的には作成されません。

使用するギャラリーとイメージ定義がまだない場合は、最初に作成します。 まず、ギャラリーを作成します。

```powershell
# Gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>テンプレートをダウンロードし、構成する

.Json テンプレートをダウンロードし、変数を使用して構成します。

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

テンプレートはサービスに提出する必要があります。このサービスによって、スクリプトなど、あらゆる依存成果物がダウンロードされ、*IT_* で始まるステージング リソース グループに保存されます。

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -ApiVersion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

イメージをビルドするには、テンプレート上で "Run" を呼び出す必要があります。

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

イメージを作成し、両方のリージョンにレプリケートするまでにしばらくかかることがあります。 この部分が終了するまで待ってから、VM の作成に進みます。

イメージ ビルド ステータスの取得を自動化するためのオプションについては、Readme をご覧ください
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>VM の作成

Azure Image Builder で作成されたイメージ バージョンから VM を作成します。

作成したイメージ バージョンを取得します。
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

イメージがレプリケートされた 2 番目のリージョンで VM を作成します。

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>カスタマイズの確認
VM を作成したときに設定したユーザー名とパスワードを使用して、VM へのリモート デスクトップ接続を作成します。 VM 内で、コマンド プロンプトを開き、次のように入力します。

```console
dir c:\
```

イメージのカスタマイズ中に作成された `buildActions` という名前のディレクトリが表示されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする
ここでイメージ バージョンを再度カスタマイズして同じイメージの新しいバージョンを作成してみる場合は、**このステップをスキップ** して、[Azure Image Builder を使用した別のイメージ バージョンの作成](image-builder-gallery-update-image-version.md)に関するページに進みます。


これにより、他のすべてのリソース ファイルとともに、作成されたイメージが削除されます。 リソースを削除する前に、このデプロイを終了していることを確認します。

まず、リソース グループ テンプレートを削除します。削除しないと、AIB で使用されているステージング リソース グループ (*IT_* ) が消去されません。

イメージ テンプレートの ResourceID を取得します。 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

イメージ テンプレートを削除します。

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

ロール割り当ての削除

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

定義の削除

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

ID の削除

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

リソース グループを削除します。

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

作成したイメージ バージョンを更新する方法については、[Azure Image Builder を使用して別のイメージ バージョンを作成する方法](image-builder-gallery-update-image-version.md)に関する記事をご覧ください。
