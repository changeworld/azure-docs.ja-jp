---
title: Azure Stack の Azure ID を検証する | Microsoft Docs
description: Azure Stack 適合性チェッカーを使用して、Azure ID を検証します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9c7ac89d1f12e8ec033b201f2c2dd845c11486e2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077819"
---
# <a name="validate-azure-identity"></a>Azure ID の検証 
Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を使用して、対象の Azure Active Directory (Azure AD) を Azure Stack で使用する準備が整っていることを検証します。 Azure Stack のデプロイを開始する前に、Azure ID ソリューションを検証します。  

適合性チェッカーは以下を検証します。
 - Azure Stack の ID プロバイダーとしての Azure Active Directory (Azure AD)。
 - 使用予定の Azure AD アカウントに、Azure Active Directory のグローバル管理者としてログオンできること。 

Azure Stack のユーザー、アプリケーション、グループ、およびサービス プリンシパルに関する情報を Azure AD に格納する準備が、お使いの環境に整っていることを検証によって確認できます。

## <a name="get-the-readiness-checker-tool"></a>適合性チェッカー ツールを取得する
最新バージョンの Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を [PSGallery](https://aka.ms/AzsReadinessChecker) からダウンロードします。  

## <a name="prerequisites"></a>前提条件
次の前提条件を満たす必要があります。

**ツールを実行するコンピューター:**
 - インターネットに接続された Windows 10 または Windows Server 2016。
 - PowerShell 5.1 以降。 お使いのバージョンを確認するには、次の PowerShell コマンドを実行し、"*メジャー*" バージョンと "*マイナー*" バージョンを確かめます。  

   > `$PSVersionTable.PSVersion`
 - [PowerShell for Azure Stack](azure-stack-powershell-install.md) を構成します。 
 - 最新バージョンの [Microsoft Azure Stack 適合性チェッカー](https://aka.ms/AzsReadinessChecker) ツール。

**Azure Active Directory の環境:**
 - Azure Stack に使用する Azure AD アカウントを特定し、それが Azure Active Directory グローバル管理者であることを確認します。
 - Azure AD テナントの名前を特定します。 テナント名は、Azure Active Directory の "*プライマリ*" ドメイン名である必要があります  (例: *contoso.onmicrosoft.com*)。 
 - 使用する AzureEnvironment を特定します: *AzureCloud*、*AzureGermanCloud*、または *AzureChinaCloud*。

## <a name="validate-azure-identity"></a>Azure ID の検証 
1. 前提条件を満たしているコンピューターで、管理 PowerShell プロンプトを開き、次のコマンドを実行して、AzsReadinessChecker をインストールします。  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. PowerShell プロンプトから次を実行して、*$serviceAdminCredential* を、お使いの Azure AD テナントのサービス管理者として設定します。  *serviceadmin@contoso.onmicrosoft.com* を、お使いのアカウントおよびテナントで置き換えます。 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. PowerShell プロンプトから次を実行して、Azure AD の検証を開始します。 
   - AzureEnvironment の値を *AzureCloud*、*AzureGermanCloud*、または *AzureChinaCloud* として指定します。  
   - Azure Active Directory テナント名を指定して、*contoso.onmicrosoft.com* で置き換えます。 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. ツールの実行後、出力を確認します。 ログオンとインストール要件の両方について、状態が **OK** であることを確認します。 次の図のように、検証が成功したことが表示されます。 
 
![run-validation](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>レポートとログ ファイル
検証を実行するたびに、結果のログが **AzsReadinessChecker.log** と **AzsReadinessCheckerReport.json** に出力されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

これらのファイルは、Azure Stack をデプロイする前、または検証に関する問題を調査する前に、検証の状態を共有するときに役立ちます。  両方のファイルに、以降の各検証チェックの結果が保持されます。 デプロイ チームはこのレポートを使用して ID 構成を確認できます。 デプロイ チームやサポート チームは、検証の問題を調査する際に、このログ ファイルを役立たせることができます。 

既定では、両方のファイルが *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json* に書き込まれます。  
 - 別のレポートの場所を指定するには、実行コマンド ラインの末尾で **-OutputPath** ***&lt;パス&gt;*** パラメーターを使用します。   
 - ツールの以前の実行に関する情報を *AzsReadinessCheckerReport.json* からクリアするには、実行コマンドの末尾で **-CleanReport**   パラメーターを使用します。 

詳細については、「[Azure Stack validation report (Azure Stack 検証レポート)](azure-stack-validation-report.md)」を参照してください。

## <a name="validation-failures"></a>検証エラー
検証チェックに失敗した場合は、エラーの詳細が PowerShell ウィンドウに表示されます。 また、ツールによって、AzsReadinessChecker.log にログ情報が記録されます。

次の例は、一般的な検証エラーに関するガイダンスです。

### <a name="expired-or-temporary-password"></a>期限切れまたは一時パスワード 
 
![期限切れのパスワード](./media/azure-stack-validate-identity/expired-password.png)
**原因** - パスワードの有効期限が切れているか、一時パスワードであるため、アカウントがログインできません。     

**解決策** - PowerShell で次を実行し、プロンプトに従ってパスワードをリセットします。  
> `Login-AzureRMAccount`

または、 https://portal.azure.com にアカウントとしてログインします。この場合、ユーザーはパスワードの変更を強制されます。
### <a name="unknown-user-type"></a>ユーザーの種類が不明 
 
![不明なユーザー](./media/azure-stack-validate-identity/unknown-user.png)
**原因** -指定した Azure Active Directory (AADDirectoryTenantName) にアカウントがログオンできません。 この例では、*AzureChinaCloud* が *AzureEnvironment* として指定されています。

**解決策** - 指定した Azure 環境に対してアカウントが有効であることを確認します。 PowerShell では、次を実行して、特定の環境に対してアカウントが有効であることを確認します: Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>アカウントが管理者ではない 
 
![管理者ではない](./media/azure-stack-validate-identity/not-admin.png)

**原因** - アカウントは正常にログオンできますが、そのアカウントが Azure Active Directory (AADDirectoryTenantName) の管理者ではありません。  

**解決策** - https://portal.azure.com にアカウントとしてログインし、**[Azure Active Directory]** > **[ユーザー]** > *[ユーザーの選択]* > **[ディレクトリ ロール]** に移動して、ユーザーが**グローバル管理者**であることを確認します。  アカウントがユーザーである場合は、**[Azure Active Directory]** > **[カスタム ドメイン]** の名前に移動し、*AADDirectoryTenantName* に対して指定した名前が、このディレクトリのプライマリ ドメイン名としてマークされていることを確認します。  この例では、*contoso.onmicrosoft.com* です。 

Azure Stack では、ドメイン名がプライマリ ドメイン名である必要があります。

## <a name="next-steps"></a>次の手順
[Azure の登録を検証する](azure-stack-validate-registration.md)  
[対応状況レポートを表示する](azure-stack-validation-report.md)  
[Azure Stack の統合に関する一般的な考慮事項](azure-stack-datacenter-integration.md)  

