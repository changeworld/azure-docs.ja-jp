---
title: Azure Automation と Azure DevOps Services ソース管理の統合
description: Azure Automation アカウントと Azure DevOps Services ソース管理の統合を設定するシナリオについて説明します。
services: automation
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, Azure DevOps Services, ソース管理, 自動化
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801439"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Azure Automation のシナリオ - Automation ソース管理と Azure DevOps の統合

> [!NOTE]
> ソース管理の新しいエクスペリエンスがあります。 新しいエクスペリエンスの詳細については、[ソース管理 (プレビュー)](source-control-integration.md) に関するページを参照してください。

このシナリオには Azure DevOps プロジェクトがあり、それを、ソース管理の対象となる Azure Automation の Runbook または DSC 構成の管理に使用しています。

この記事では、チェックインごとに継続的インテグレーションが発生するように、Azure DevOps と Azure Automation 環境を統合する方法について説明します。

## <a name="getting-the-scenario"></a>シナリオの取得

このシナリオは、Azure Portal の [Runbook ギャラリー](automation-runbook-gallery.md)から直接インポート、または [PowerShell ギャラリー](https://www.powershellgallery.com)からダウンロードできる 2 つの PowerShell Runbook で構成されています。

### <a name="runbooks"></a>Runbooks

Runbook | 説明|
--------|------------|
Sync-VSTS | チェックインの処理が完了したら、Azure DevOps ソース管理から Runbook または構成をインポートします。 手動で実行した場合、すべての Runbook または構成がインポートされ Automation アカウントに発行されます。| 
Sync-VSTSGit | チェックインの処理が完了したら、Git ソース管理下にある Azure DevOps ソース管理から Runbook または構成をインポートします。 手動で実行した場合、すべての Runbook または構成がインポートされ Automation アカウントに発行されます。|

### <a name="variables"></a>variables

可変 | 説明|
-----------|------------|
VSToken | 作成する変数資産 (Azure DevOps 個人用アクセス トークンを含む) をセキュリティで保護します。 Azure DevOps 個人用アクセス トークンを作成する方法については、[Azure DevOps 認証に関するページ](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)に説明があります。

## <a name="installing-and-configuring-this-scenario"></a>このシナリオのインストールと構成

Runbook または構成を Automation アカウントに同期するために使用する Azure DevOps で、[個人用アクセス トークン](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)を作成します。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Runbook が Azure DevOps に対して認証を行い、Runbook または構成を Automation アカウントに同期できるように、個人用アクセス トークンを保持する Automation アカウントで、[セキュリティで保護された変数](automation-variables.md)を作成します。 これには VSToken という名前を付けることができます。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Runbook または構成を Automation アカウントに同期する Runbook をインポートします。 Azure DevOps ソース管理または Git で Azure DevOps を使用して Automation アカウントにデプロイするかどうかに応じて、[Azure DevOps サンプル Runbook](https://www.powershellgallery.com/packages/Sync-VSTS)、または [PowerShellGallery](https://www.powershellgallery.com) にある [Git サンプル Runbook で Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTSGit) を使用できます。

![PowerShell ギャラリー](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

これで、この Runbook を[発行](automation-creating-importing-runbook.md#publishing-a-runbook)できるようになったので、Webhook を作成できます。

![Runbook の発行](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

この Sync-VSTS Runbook の [Webhook](automation-webhooks.md) を作成し、次に示すようにパラメーターを入力します。 Azure DevOps のサービス フックで必要なので、Webhook の URL をコピーします。 VSAccessTokenVariableName は、個人用アクセス トークンを保持するために前に作成したセキュリティで保護された変数の名前 (VSToken) です。

Azure DevOps (Sync-VSTS.ps1) との統合により、以下のパラメーターが取得されます。

### <a name="sync-vsts-parameters"></a>Sync-VSTS パラメーター

パラメーター | 説明|
--------|------------|
WebhookData | これには、Azure DevOps サービス フックから送信されたチェックイン情報が含まれます。 このパラメーターは空白のままにする必要があります。| 
ResourceGroup | Automation アカウントがあるリソース グループの名前です。|
AutomationAccountName | Azure DevOps と同期する Automation アカウントの名前です。|
VSFolder | Runbook と構成が存在する Azure DevOps 内のフォルダーの名前です。|
VSAccount | Azure DevOps 組織の名前です。|
VSAccessTokenVariableName | Azure DevOps 個人用アクセス トークンを保持する、セキュリティで保護された変数 (VSToken) の名前です。|

![Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

GIT (Sync-VSTSGit.ps1) と共に Azure DevOps を使用すると、以下のパラメーターが取得されます。

パラメーター | 説明|
--------|------------|
WebhookData | これには、Azure DevOps サービス フックから送信されたチェックイン情報が含まれます。 このパラメーターは空白のままにする必要があります。|
ResourceGroup | Automation アカウントがあるリソース グループの名前です。|
AutomationAccountName | Azure DevOps と同期する Automation アカウントの名前です。|
VSAccount | Azure DevOps 組織の名前です。|
VSProject | Runbook と構成が存在する Azure DevOps 内のプロジェクトの名前です。|
GitRepo | Git リポジトリの名前です。|
GitBranch | Azure DevOps Git リポジトリの分岐の名前です。|
フォルダー | Azure DevOps Git 分岐のフォルダーの名前です。|
VSAccessTokenVariableName | Azure DevOps 個人用アクセス トークンを保持する、セキュリティで保護された変数 (VSToken) の名前です。|

![Git Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

コードのチェックイン時にこの Webhook をトリガーするフォルダーにチェックインするため、Azure DevOps 内にサービス フックを作成します。 新しいサブスクリプションを作成するとき、統合する **Web フック**をサービスとして選択します。 サービス フックの詳細については、[Azure DevOps のサービス フックに関するドキュメント](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started)を参照してください。
![サービス フック](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

これで、Azure DevOps に対する Runbook と構成のチェックインをすべて実行し、これらが Automation アカウントに自動的に同期されるようにできるはずです。

![Runbook 出力の同期](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Azure DevOps によるトリガーではなく手動でこの Runbook を実行する場合は、webhookdata パラメーターを空のままにすることができます。これにより、指定した Azure DevOps フォルダーから完全な同期が行われます。

シナリオをアンインストールする場合は、Azure DevOps からサービス フックを削除し、Runbook を削除して、VSToken 変数を削除します。
