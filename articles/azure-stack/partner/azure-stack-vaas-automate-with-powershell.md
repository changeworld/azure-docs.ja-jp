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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: aa33db56910871891003866ef62431cfd7095d2a
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954128"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>PowerShell を使用した Azure Stack の検証の自動化

サービスとしての検証 (VaaS) には、**LaunchVaaSTests.ps1** スクリプトを使ってテストの開始を自動化する機能があります。

次のワークフローに PowerShell を使うことができます。

- テスト成功

このチュートリアルでは、以下の操作を行うスクリプトの作成方法について説明します。

> [!div class="checklist"]
> * 前提条件のインストール
> * ローカル エージェントのインストールと起動
> * テストのカテゴリ (統合、機能、信頼性など) の立ち上げ
> * テストの結果のレポート

## <a name="launch-the-test-pass-workflow"></a>テスト成功ワークフローの起動

1. 管理者特権の PowerShell プロンプトを開きます。

2. 次のスクリプトを実行して、オートメーション スクリプトをダウンロードします。

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. 適切なパラメーター値を使用して、次のスクリプトを実行します。

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **パラメーター**

    | パラメーター | 説明 |
    | --- | --- |
    | VaaSUserld | VaaS ユーザー ID。 |
    | VaaSUserPassword | VaaS パスワード。 |
    | VaaSAccountTenantId | VaaS テナントの GUID。 |
    | VaaSSolutionName | テスト成功を実行する VaaS ソリューションの名前。 |
    | VaaSTestPassName | 作成する VaaS テスト成功ワークフローの名前。 |
    | VaaSTestCategories | `Integration`、`Functional`、または  `Reliability` 複数の値を使用する場合は、それぞれの値をコンマで区切ってください。  |
    | ServiceAdminUserName | Azure Stack サービス管理者アカウント。  |
    | ServiceAdminPassword | Azure Stack サービスのパスワード。  |
    | TenantAdminUserName | プライマリ テナントの管理者。  |
    | TenantAdminPassword | プライマリ テナントのパスワード。  |
    | CloudAdminUserName | クラウド管理者のユーザー名。  |
    | CloudAdminPassword | クラウド管理者のパスワード。  |

4. テストの結果を確認します。 他のオプションについては、「[VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Stack での PowerShell の詳細については、最新のモジュールを参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.5.0)
