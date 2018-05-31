---
title: Azure Stack ユーザーの PowerShell 環境の構成 | Microsoft Docs
description: Azure Stack ユーザーの PowerShell 環境の構成
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258024"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Azure Stack ユーザーの PowerShell 環境の構成

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

この記事の指示に従い、Azure Stack ユーザーの PowerShell 環境を構成します。
環境の構成後、PowerShell を使用して Azure Stack リソースを管理できます。 たとえば、PowerShell を使用し、オファーをサブスクライブしたり、仮想マシンを作成したり、Azure Resource Manager テンプレートをデプロイしたりできます。

>[!NOTE]
>この記事では、Azure Stack ユーザー環境について説明します。 クラウド オペレーター環境用に PowerShell を設定する場合、[Azure Stack オペレーターの PowerShell 環境の構成](../azure-stack-powershell-configure-admin.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

次の前提条件は[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から構成するか、[VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合、Windows ベースの外部クライアントから構成できます。

* [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。
* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>ユーザー環境の構成と Azure Stack へのサインイン

Azure Stack デプロイのタイプ (Azure AD または AD FS) に基づいて、次のいずれかのスクリプトを実行して Azure Stack 用に PowerShell を構成します。

次のスクリプト変数を自分の Azure Stack 構成の値に必ず変更してください。

* AAD tenantName
* GraphAudience エンドポイント
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) ベースのデプロイ

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory フェデレーション サービス (AD FS) ベースのデプロイ

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>リソース プロバイダーを登録する

ポータル経由でリソースがデプロイされていない新しいユーザー サブスクリプションの場合、リソース プロバイダーが自動登録されません。 次のスクリプトを実行し、リソース プロバイダーを明示的に登録できます。

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>接続のテスト

すべてが整ったら、PowerShell を使って接続をテストし、Azure Stack でリソースを作成します。 テストとして、アプリケーションのリソース グループを作成し、仮想マシンを追加します。 次のコマンドを実行し、"MyResourceGroup" という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>次の手順

* [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)
* [PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
