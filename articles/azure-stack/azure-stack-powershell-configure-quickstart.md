---
title: "PowerShell for Azure Stack のインストールと構成のクイックスタート | Microsoft Docs"
description: "PowerShell for Azure Stack のインストールと構成について説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: mabrigg
ms.openlocfilehash: ca61562607da274f0e0c7f504d1a24723210f2f7
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
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
Get-Module -ListAvailable -Name Azure* | `
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
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS ベースのデプロイ用に PowerShell を設定する

インターネットに接続した状態で Azure Stack を操作している場合は、次のスクリプトを使用できます。 ただし、インターネットに接続しない状態で Azure Stack を操作している場合は、[接続が切断された状態での PowerShell のインストール方法](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)とコマンドレットを使用して PowerShell を構成すると、このスクリプトで示すのと同様になります。 Azure Stack Development Kit にサインインするか、VPN 経由で接続している場合は Windows ベースの外部クライアントにサインインします。 管理者特権の PowerShell ISE セッションを開き、次のスクリプトを実行します。 必要に応じて、**ArmEndpoint** 変数と **GraphAudience** 変数をお使いの環境の構成に合わせて更新してください。

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
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
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

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

## <a name="next-steps"></a>次の手順

* [CLI のインストールと構成](azure-stack-connect-cli.md)

* [テンプレートの開発](user/azure-stack-develop-templates.md)







