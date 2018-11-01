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
ms.date: 10/24/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: bbd20df7f002d6072110e3b94887bac24ce13cd2
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087442"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>オペレーターとして PowerShell を使用して Azure Stack に接続する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

PowerShell を使用してオファー、プラン、クォータ、アラートの作成などのリソース管理を行うように、Azure Stack を構成できます。 このトピックは、オペレーター環境を構成するために役立ちます。

## <a name="prerequisites"></a>前提条件

[開発キット](.\asdk\asdk-connect.md#connect-with-rdp)から、または [VPN 経由で ASDK に接続](.\asdk\asdk-connect.md#connect-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。 

 - [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  
 - [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>オペレーター環境の構成と Azure Stack へのサインイン

PowerShell を使用する Azure Stack オペレーター環境を構成します。 次のいずれかのスクリプトを実行します。Azure AD tenantName、GraphAudience エンドポイント、ArmEndpoint の値は、実際の環境の構成に従って置き換えてください。

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>接続のテスト

必要な設定がすべて整ったら、PowerShell を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、**MyResourceGroup** という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>次の手順

 - [Azure Stack のテンプレートの開発](user/azure-stack-develop-templates.md)
 - [PowerShell を使用したテンプレートのデプロイ](user/azure-stack-deploy-template-powershell.md)