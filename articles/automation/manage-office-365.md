---
title: Azure Automation を使用して Office 365 サービスを管理する
description: Azure Automation を使用して Office 365 サブスクリプション サービスを管理する方法について説明します。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80576770"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Azure Automation を使用して Office 365 サービスを管理する

Azure Automation は、Microsoft Word や Microsoft Outlook などの製品に関する、Office 365 サブスクリプション サービスの管理に使用できます。 Office 365 との対話は [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) によって有効になります。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」を参照してください。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="prerequisites"></a>前提条件

Azure Automation で Office 365 サブスクリプション サービスを管理するには、次のものが必要です。

* Azure サブスクリプション。 「[サブスクリプション決定ガイド](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)」を参照してください。
* ユーザー アカウントの資格情報と Runbook を保持する、Azure の Automation オブジェクト。 「[Azure Automation の概要](https://docs.microsoft.com/azure/automation/automation-intro)」を参照してください。
* Azure AD。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」を参照してください。
* アカウントを持つ Office 365 テナント。 「[Office 365 テナントのセットアップ](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)」を参照してください。

## <a name="installing-the-msonline-and-msonlineext-modules"></a>MSOnline と MSOnlineExt モジュールのインストール

Azure Automation で Office 365 を使用するには、Windows PowerShell 用 Microsoft Azure Active Directory (`MSOnline` モジュール) が必要です。 また、シングルおよびマルチ テナント環境での Azure AD 管理を簡素化するモジュール [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) も必要です。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」の説明に従ってこれらのモジュールをインストールしてください。

>[!NOTE]
>MSOnline PowerShell を使用するには、Azure AD のメンバーである必要があります。 ゲスト ユーザーはモジュールを使用できません。

## <a name="creating-an-azure-automation-account"></a>Azure Automation アカウントの作成

この記事の手順を完了するには、Azure Automation のアカウントが必要です。 「[Azure Automation アカウントを作成する](automation-quickstart-create-account.md)」を参照してください。
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>資産としての MSOnline および MSOnlineExt の追加

次に、インストールされている MSOnline と MSOnlineExt モジュールを追加して、Office 365 の機能を有効にします。 「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。

1. Azure portal で、 **[Automation アカウント]** を選択します。
2. お使いの Automation アカウントを選択します。
3. **[共有リソース]** で **[モジュール ギャラリー]** を選択します。
4. MSOnline を検索します。
5. `MSOnline` PowerShell モジュールを選択し、 **[インポート]** をクリックして、モジュールを資産としてインポートします。
6. 手順 4 と 5 を繰り返して、`MSOnlineExt` モジュールを見つけてインポートします。 

## <a name="creating-a-credential-asset-optional"></a>資格情報資産の作成 (省略可能)

スクリプトを実行するアクセス許可を持つ Office 365 管理ユーザーの資格情報資産を作成するかどうかは任意です。 ただし、これは、PowerShell スクリプト内でユーザー名とパスワードが公開されないようにするのに役立ちます。 手順については、「[資格情報資産の作成](automation-use-azure-ad.md#creating-a-credential-asset)」を参照してください。

## <a name="creating-an-office-365-service-account"></a>Office 365 サービス アカウントの作成

Office 365 サブスクリプション サービスを実行するには、必要な操作を実行するためのアクセス許可を持つ Office 365 サービス アカウントが必要です。 1 つの全体管理者アカウントを使用するか、サービスごとに 1 つのアカウントを使用するか、1 つの関数またはスクリプトを実行できます。 いずれの場合も、サービス アカウントには複雑でセキュリティで保護されたパスワードが必要です。 「[一般法人向け Office 365 のセットアップ](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)」を参照してください。 

## <a name="connecting-to-the-azure-ad-online-service"></a>Azure AD オンライン サービスへの接続

>[!NOTE]
>MSOnline モジュール コマンドレットを使用するには、それらを Windows PowerShell から実行する必要があります。 PowerShell Core では、これらのコマンドレットはサポートされていません。

MSOnline モジュールを使用して、Office 365 サブスクリプションから Azure AD に接続することができます。 この接続では、Office 365 のユーザー名とパスワードを使用するか、多要素認証 (MFA) を使用します。 Azure portal または Windows PowerShell コマンド プロンプトを使用して接続できます (管理者特権は必要ありません)。

PowerShell の例を以下に示します。 [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) コマンドレットは、資格情報の入力を求め、それらを `Msolcred` 変数に格納します。 その後、[Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) コマンドレットは、その資格情報を使用してAzure ディレクトリ オンライン サービスに接続します。 特定の Azure 環境に接続したい場合は、`AzureEnvironment` パラメーターを使用します。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

エラーが表示されない場合は、正常に接続されています。 簡単なテストとして、Office 365 コマンドレット (`Get-MsolUser` など) を実行して結果を確認します。 エラーが表示された場合、一般的な問題はパスワードが正しくないことです。

>[!NOTE]
>AzureRM モジュールまたは Az モジュールを使用して、Office 365 サブスクリプションから Azure AD に接続することもできます。 メインの接続コマンドレットは [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0) です。 このコマンドレットは、特定の Office 365 環境の `AzureEnvironmentName` パラメーターをサポートしています。

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>既存のスクリプトから PowerShell Runbook を作成する

PowerShell スクリプトから Office 365 の機能にアクセスします。 ユーザー名 `admin@TenantOne.com` を持つ `Office-Credentials` という名前の資格情報に対するスクリプトの例を次に示します。 これは、`Get-AutomationPSCredential` を使用して Office 365 資格情報をインポートします。

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Runbook でのスクリプトの実行

お使いのスクリプトを Azure Automation Runbook で使用できます。 例として、PowerShell の Runbook の種類を使用します。

1. 新しい PowerShell Runbook を作成します。 「[Azure Automation Runbook を作成する](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)」を参照してください。
2. お使いの Automation アカウントから、 **[プロセスの自動化]** の下にある **[Runbook]** を選択します。
3. 新しい Runbook を選択し、 **[編集]** をクリックします。
4. お使いのスクリプトをコピーし、Runbook のテキストエディターに貼り付けます。
5. **[資産]** を選択し、 **[資格情報]** を展開して、Office 365 資格情報があることを確認します。
6. **[保存]** をクリックします。
7. **[テスト] ペイン**を選択し、 **[開始]** をクリックして Runbook のテストを開始します。 「[Azure Automation で Runbook を管理する](https://docs.microsoft.com/azure/automation/manage-runbooks)」を参照してください。
8. テストが完了したら、[テスト] ペインから終了します。

## <a name="publishing-and-scheduling-the-runbook"></a>Runbook の発行とスケジュール設定

Runbook を発行してスケジュールを設定するには、「[Azure Automation で Runbook を管理する](https://docs.microsoft.com/azure/automation/manage-runbooks)」を参照してください

## <a name="next-steps"></a>次のステップ

* Automation の資格情報資産については、「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。
* Automation モジュールの使用方法については、「[Azure Automation でのモジュールの管理](shared-resources/modules.md)」を参照してください。
* Runbook の管理の概要については「[Azure Automation で Runbook を管理する](https://docs.microsoft.com/azure/automation/manage-runbooks)」を参照してください。
* Azure Automation で Runbook を開始するために使用できる方法について詳しくは、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)を参照してください。