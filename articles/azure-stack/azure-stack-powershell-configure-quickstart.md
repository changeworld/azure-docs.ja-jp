---
title: "PowerShell for Azure Stack のインストールと構成のクイックスタート | Microsoft Docs"
description: "PowerShell for Azure Stack のインストールと構成について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: sngun
ms.openlocfilehash: 039806e164be29b80e604bbcf0f2997e635664e5
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Azure Stack での PowerShell を使用した準備と実行

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

このクイック スタートは、PowerShell を使用して Azure Stack 環境をインストールして構成する際に役立ちます。 この記事で提供するスクリプトは、**Azure Stack オペレーター**のみを対象としています。

この記事は、[PowerShell のインストール]( azure-stack-powershell-install.md)に関する記事、[ツールのダウンロード]( azure-stack-powershell-download.md)に関する記事、「[Azure Stack オペレーターの PowerShell 環境の構成]( azure-stack-powershell-configure-admin.md)」で説明されている手順を要約したバージョンです。 このトピックのスクリプトを使用すると、Azure Active Directory または Active Directory フェデレーション サービス (AD FS) によってデプロイされている Azure Stack 環境用に PowerShell を設定できます。  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Azure Active Directory ベースのデプロイ用に PowerShell を設定する

Azure Stack Development Kit にサインインするか、VPN 経由で接続している場合は Windows ベースの外部クライアントにサインインします。 管理者特権の PowerShell ISE セッションを開き、次のスクリプトを実行します。 必要に応じて、**TenantName**、**ArmEndpoint**、**GraphAudience** 変数をお使いの環境の構成に合わせて更新してください。

> [!IMPORTANT]
> AzureRM 1.2.11 リリースの PowerShell モジュールには、重大な変更が多数追加されています。 1.2.10 バージョンからアップグレードするには、[移行ガイド](https://aka.ms/azspowershellmigration)を参照してください。

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS ベースのデプロイ用に PowerShell を設定する 

Azure Stack Development Kit にサインインするか、VPN 経由で接続している場合は Windows ベースの外部クライアントにサインインします。 管理者特権の PowerShell ISE セッションを開き、次のスクリプトを実行します。 必要に応じて、**ArmEndpoint** 変数と **GraphAudience** 変数をお使いの環境の構成に合わせて更新してください。

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>接続のテスト

PowerShell を構成したので、リソース グループを作成して構成をテストすることができます。

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

リソース グループが作成されると、**[Provisioning state]\(プロビジョニング状態\)** プロパティは **[Succeeded]\(成功\)** に設定されます。

## <a name="next-steps"></a>次のステップ

* [CLI のインストールと構成](azure-stack-connect-cli.md)

* [テンプレートの開発](user/azure-stack-develop-templates.md)







