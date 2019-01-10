---
title: オペレーターとして PowerShell を使用して Azure Stack に接続する | Microsoft Docs
description: オペレーターとして PowerShell を使用して Azure Stack に接続する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 7a744520fe2a3b53b1306d4c80a5eca7d86258a7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104541"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>オペレーターとして PowerShell を使用して Azure Stack に接続する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

PowerShell を使用してオファー、プラン、クォータ、アラートの作成などのリソース管理を行うように、Azure Stack を構成できます。 このトピックは、オペレーター環境を構成するために役立ちます。

## <a name="prerequisites"></a>前提条件

[開発キット](./asdk/asdk-connect.md#connect-with-rdp)から、または [VPN 経由で ASDK に接続](./asdk/asdk-connect.md#connect-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。 

 - [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  
 - [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

## <a name="connect-with-azure-ad"></a>Azure AD との接続

PowerShell を使用する Azure Stack オペレーター環境を構成します。 次のスクリプトのいずれかを実行します。Azure Active Directory (Azure AD) tenantName と Azure Resource Manager エンドポイント値を独自の環境構成で置き換えます。 <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>AD FS を使用した接続

Azure Stack オペレーター環境に Active Directory フェデレーション サービス (Azure AD FS) を使用した PowerShell で接続します。 Azure Stack Development Kit では、この Azure Resource Manager エンドポイントは `https://adminmanagement.local.azurestack.external` に設定されています。 Azure Resource Manager エンドポイントを Azure Stack 統合システム用に取得するには、サービス プロバイダーに問い合わせてください。

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId `
    -Credential $cred
  ```



## <a name="test-the-connectivity"></a>接続のテスト

必要な設定がすべて整ったら、PowerShell を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、**MyResourceGroup** という名前のリソース グループを作成します。

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>次の手順

 - [Azure Stack のテンプレートの開発](user/azure-stack-develop-templates.md)
 - [PowerShell を使用したテンプレートのデプロイ](user/azure-stack-deploy-template-powershell.md)
