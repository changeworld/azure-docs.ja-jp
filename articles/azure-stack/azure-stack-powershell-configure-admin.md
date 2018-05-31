---
title: Azure Stack の PowerShell 環境の構成 | Microsoft Docs
description: Azure Stack の PowerShell 環境の構成方法について説明します。
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
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076472"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Azure Stack の PowerShell 環境を構成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

PowerShell を使用してオファー、プラン、クォータ、アラートの作成などのリソース管理を行うように、Azure Stack を構成できます。 このトピックは、オペレーター環境を構成するために役立ちます。

## <a name="prerequisites"></a>前提条件

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。 

 - [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  
 - [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>オペレーター環境の構成と Azure Stack へのサインイン

PowerShell を使用する Azure Stack オペレーター環境を構成します。 デプロイのタイプ (Azure AD または AD FS) に基づいて、次のいずれかのスクリプトを実行します (環境の構成に従って、必ず Azure AD tenantName、GraphAudience エンドポイント、および ArmEndpoint の値を置き換えてください)。

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD) ベースのデプロイ

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory フェデレーション サービス (AD FS) ベースのデプロイ

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>接続のテスト

必要な設定がすべて整ったら、PowerShell を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>次の手順
 - [Azure Stack のテンプレートの開発](user/azure-stack-develop-templates.md)
 - [PowerShell を使用したテンプレートのデプロイ](user/azure-stack-deploy-template-powershell.md)
