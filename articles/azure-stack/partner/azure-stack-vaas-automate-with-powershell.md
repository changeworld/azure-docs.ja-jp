---
title: PowerShell を使用した Azure Stack の検証の自動化 | Microsoft Docs
description: Azure Stack の検証は、PowerShell を使って自動化することができます。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234474"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>PowerShell を使用した Azure Stack の検証の自動化 

サービスとしての検証 (VaaS) には、**LaunchVaaSTests.ps1** スクリプトを使ってテストの開始を自動化する機能があります。

次のワークフローに PowerShell を使うことができます。

- テスト パス ワークフロー

このスクリプトには、ワークフローの 4 つの要素が含まれています。

- 前提条件のインストール。
- ローカル エージェントのインストールと起動。
- テストのカテゴリ (統合、機能、信頼性など) の立ち上げ。
- 各テスト パスの結果報告 (監視とレポート ファイルの生成)。

## <a name="launch-the-test-pass-workflow"></a>テスト パス ワークフローの起動

1. 管理者特権の PowerShell プロンプトを開きます。

2. 次のスクリプトを実行して、オートメーション スクリプトをダウンロードします。

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. 次のスクリプトに実際の値を指定して実行します。

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **パラメーター**

    | パラメーター | 説明 |
    | --- | --- |
    | VaaSUserld | VaaS ユーザー ID。 | 
    | VaaSUserPassword | VaaS パスワード。 |
    | ServiceAdminUser | Azure Stack サービス管理者アカウント。  |
    | ServiceAdminPassword | Azure Stack サービスのパスワード。  |
    | TenantAdminUser | プライマリ テナントの管理者。  |
    | TenantAdminPassword | プライマリ テナントのパスワード。  |
    | FunctionalCategory| 統合、機能、 信頼性のいずれか。 複数の値を使用する場合は、それぞれの値をコンマで区切ってください。  |

4. テストの結果を確認します。 テスト結果の解釈については、[テストの監視](azure-stack-vaas-monitor-test.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

 - [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
 - Azure Stack での PowerShell について詳しくは、[Azure Stack Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) のリファレンスをご覧ください。