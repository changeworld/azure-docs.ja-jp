---
title: Azure Automation でのソース管理の統合
description: この記事では、Azure Automation での GitHub とのソース管理の統合について説明します。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253729"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation でのソース管理の統合

 Azure Automation でのソース管理の統合により、ソース管理リポジトリからの一方向の同期がサポートされます。 ソース管理では、GitHub または Azure Repos のソース管理リポジトリ内のスクリプトを使用して、Automation アカウントの Runbook を最新の状態に維持することができます。 この機能により、開発環境でテストされたコードを、運用の Automation アカウントに昇格することが容易になります。
 
 ソース管理を統合することで、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックが簡単になります。 たとえば、ソース管理を使用すると、開発、テスト、運用の Automation アカウントに対して、ソース管理内の異なるブランチを同期できます。 

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="source-control-types"></a>ソース管理の種類

Azure Automation は、次の 3 種類のソース管理をサポートしています。

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>前提条件

* ソース管理リポジトリ (GitHub または Azure Repos)
* [実行アカウント](manage-runas-account.md)
* **Az.Accounts** モジュール (AzureRM.Profile と同等の Az モジュール) を含む、Automation アカウントの[最新の Azure モジュール](automation-update-azure-modules.md)

> [!NOTE]
> ソース管理の同期ジョブは、ユーザーの Automation アカウントの下で実行され、その他の Automation ジョブと同じレートで課金されます。

## <a name="configuring-source-control"></a>ソース管理の構成

このセクションでは、Automation アカウントのソース管理を構成する方法について説明します。 Azure portal または PowerShell のいずれかを使用できます。

### <a name="configure-source-control----azure-portal"></a>ソース管理を構成する -- Azure portal

Azure portal を使用してソース管理を構成するには、次の手順に従います。

1. Automation アカウント内で、 **[ソース管理]** を選択し、 **[+ 追加]** をクリックします。

    ![ソース管理の選択](./media/source-control-integration/select-source-control.png)

2. **[ソース管理の種類]** を選択し、 **[認証]** をクリックします。 

3. ブラウザー ウィンドウが開き、サインインが求められます。 指示に従って認証を完了します。

4. **[ソース管理の概要]** ページのフィールドを使用して、以下で定義されているソース管理プロパティを入力します。 完了したら、 **[保存]** をクリックします。 

    |プロパティ  |説明  |
    |---------|---------|
    |ソース管理名     | ソース管理のためのフレンドリ名。 この名前は、アルファベットと数字でのみ構成されている必要があります。        |
    |ソース管理の種類     | ソース管理機構の種類。 使用できるオプションは次のとおりです。</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
    |リポジトリ     | リポジトリまたはプロジェクトの名前。 最初の 200 個のリポジトリが取得されます。 リポジトリを検索するには、フィールドに名前を入力して、 **[Search on GitHub]\(GitHub で検索\)** をクリックします。|
    |[Branch]\(ブランチ)     | ソース ファイルの抽出元のブランチ。 TFVC ソース管理の種類では、ブランチのターゲット設定は使用できません。          |
    |フォルダー パス     | 同期する Runbook を含むフォルダー (/Runbooks など)。 指定されたフォルダー内の Runbook のみが同期されます。 再帰はサポートされていません。        |
    |自動同期<sup>1</sup>     | ソース管理リポジトリでコミットが行われたときに、自動同期をオンまたはオフにする設定。        |
    |Runbook の発行     | Runbook がソース管理からの同期後に自動的に発行される場合はオン、それ以外の場合はオフの設定。           |
    |説明     | ソース管理に関する追加の詳細を指定するテキスト。        |

    <sup>1</sup> Azure Repos でソース管理の統合を構成する際に自動同期を有効にするには、プロジェクト管理者でなければなりません。

   ![ソース管理の概要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> ソース管理リポジトリのログインは、Azure portal のログインと異なる可能性があります。 ソース管理を構成する際は、ソース管理リポジトリの正しいアカウントでログインするようにしてください。 正しいかどうかが不明な場合、ブラウザーで新しいタブを開き、visualstudio.com または github.com からログアウトし、ソース管理にもう一度接続してみてください。

### <a name="configure-source-control----powershell"></a>ソース管理を構成する -- PowerShell

PowerShell を使用して Azure Automation のソース管理を構成することもできます。 この操作に PowerShell コマンドレットを使用するには、個人用アクセス トークン (PAT) が必要です。 [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) コマンドレットを使用してソース管理接続を作成します。 このコマンドレットでは、PAT に対するセキュリティで保護された文字列が必要です。 セキュリティで保護された文字列を作成する方法については、「[ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)」を参照してください。

次のサブセクションは、PowerShell による GitHub、Azure Repos (Git)、および Azure Repos (TFVC) のソース管理接続の作成について説明しています。

#### <a name="create-source-control-connection-for-github"></a>GitHub のソース管理接続を作成する

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure Repos (Git) のソース管理接続を作成する

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure Repos (TFVC) のソース管理接続を作成する

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>個人用アクセス トークン (PAT) のアクセス許可

ソース管理には、PAT に対するいくつかの最小限のアクセス許可が必要です。 次のサブセクションは、GitHub と Azure Repos で必要な最小限のアクセス許可を示します。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub の PAT に対する最小限のアクセス許可

次の表は、GitHub で必要な PAT の最小限のアクセス許可を定義したものです。 GitHub で PAT を作成する方法について詳しくは、「[コマンドライン用の個人アクセス トークンを作成する](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)」をご覧ください。

|Scope  |説明  |
|---------|---------|
|**レポジトリ**     |         |
|repo:status     | コミット状態へのアクセス         |
|repo_deployment      | デプロイ状態へのアクセス         |
|public_repo     | パブリック リポジトリへのアクセス         |
|**admin:repo_hook**     |         |
|write:repo_hook     | リポジトリ フックの書き込み         |
|read:repo_hook|リポジトリ フックの読み取り|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos の PAT に対する最小限のアクセス許可

次のリストは、Azure Repos で必要な PAT の最小限のアクセス許可を定義したものです。 Azure Repos で PAT を作成する方法について詳しくは、「[個人用アクセス トークンによるアクセスの認証](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)」をご覧ください。

| Scope  |  アクセスの種類  |
|---------| ----------|
| コード      | Read  |
| プロジェクトとチーム | Read |
| ID | Read     |
| User Profile | Read     |
| 作業項目 | Read    |
| サービス接続 | 読み取り、クエリの実行、および管理<sup>1</sup>    |

<sup>1</sup>サービス接続のアクセス許可は、自動同期を有効にした場合にのみ必要です。

## <a name="synchronizing"></a>[同期中]

ソース管理と同期するには、次の手順を実行します。 

1. **[ソース管理]** ページの表でソースを選択します。 

2. **[Start Sync] (同期の開始)** をクリックして、同期プロセスを開始します。 

3. **[同期ジョブ]** タブをクリックして、現在の同期ジョブまたは以前の同期ジョブの状態を表示します。 

4. **[ソース管理]** ドロップダウン メニューで、ソース管理機構を選択します。

    ![同期状態](./media/source-control-integration/sync-status.png)

5. ジョブをクリックすると、ジョブの出力を表示することができます。 次に、ソース管理の同期ジョブからの出力例を示します。

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

     =========================================================================

    ```

6. **[ソース管理の同期ジョブの概要]** ページの **[すべてのログ]** を選択すると、追加のログを使用できます。 このような追加のログ エントリは、ソース管理を使用する際に発生する可能性がある問題のトラブルシューティングに役立ちます。

## <a name="disconnecting-source-control"></a>ソース管理の切断

ソース管理リポジトリから切断するには:

1. Automation アカウントの **[アカウント設定]** で、 **[ソース管理]** を開きます。

2. 削除するソース管理機構を選択します。 

3. **Source Control Summary (ソース管理の概要)** ページで **削除** をクリックします。

## <a name="handling-encoding-issues"></a>エンコードに関する問題の処理

ソース管理リポジトリで複数のユーザーがさまざまなエディターを使用して Runbook を編集していると、エンコードの問題が発生する可能性があります。 この状況について詳しくは、「[エンコード問題の一般的な原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)」をご覧ください

## <a name="updating-the-pat"></a>PAT の更新

現時点では、Azure portal を使用してソース管理の PAT を更新する方法はありません。 PAT の有効期限が切れたか失効した後、次のいずれかの方法で新しいアクセス トークンを使用してソース管理を更新できます。

* [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update) を使用する。
* [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) コマンドレットを使用する。

## <a name="next-steps"></a>次のステップ

Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
