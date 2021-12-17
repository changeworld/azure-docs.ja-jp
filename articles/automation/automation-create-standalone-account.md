---
title: スタンドアロン Azure Automation アカウントを作成する
description: この記事では、スタンドアロン Azure Automation アカウントを作成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: eedee6af181a0dc99635dc455dfda6a154fe5eb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451058"
---
# <a name="create-a-standalone-azure-automation-account"></a>スタンドアロン Azure Automation アカウントを作成する

この記事では、Azure portal を使用して Azure Automation アカウントを作成する方法について説明します。 Automation アカウントを使用すると、追加の管理機能を使用したり、Azure Monitor ログを統合したりすることなく、Automation について評価し、学ぶことができます。 管理機能の追加や Azure Monitor ログの統合は、Runbook ジョブを詳細に監視するために、後からいつでも行うことができます。

Automation アカウントを使うと、Azure Resource Manager またはクラシック デプロイ モデルでリソースを管理することで、Runbook を認証できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。

このアカウントが作成されると、自動化のニーズを満たす Runbook の作成とデプロイをすばやく開始することができます。

## <a name="permissions-required-to-create-an-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可

Automation アカウントを作成または更新したり、この記事で説明されているタスクを完了したりするには、次の特権とアクセス許可が必要です。

Automation アカウントを作成するには、Azure AD ユーザー アカウントが、`Microsoft.Automation` リソースの所有者ロールに相当するアクセス許可を持つロールに追加されている必要があります。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure Portal で新しい Automation アカウントを作成する

Azure Portal で Azure Automation アカウントを作成するには、以下の手順を実行します。

1. サブスクリプション管理者ロールのメンバーであり、かつサブスクリプションの共同管理者であるアカウントを使用して Azure portal にサインインします。
1. **[+ リソースの作成]** を選択します。
1. 「**Automation**」を検索します。 検索結果で、 **[Automation]** を選択します。

   :::image type="content" source="./media/automation-create-standalone-account/automation-account-portal.png" alt-text="ポータルでの Automation アカウントの検索":::

新しい Automation アカウントのオプションは、 **[Automation アカウントを作成する]** ページのタブにまとめられています。 次のセクションでは、各タブとそのオプションについて説明します。

### <a name="basics"></a>基本

**[基本]** タブでは、Automation アカウントに関する必須の情報を入力します。 **[基本]** タブの設定が済んだら、他のタブのオプションを設定して新しい Automation アカウントをさらにカスタマイズするか、 **[確認および作成]** を選んで既定のオプションをそのまま使い、アカウントの検証と作成を続けることができます。

> [!NOTE]
> 既定では、Automation アカウントのシステム割り当てマネージド ID が有効になっています。

次の表では **[基本]** タブのフィールドについて説明します。

| **フィールド** | **必須**<br> **or**<br> **省略可能** |**説明** |
|---|---|---|
|サブスクリプション|必須 |ドロップダウン リストから、アカウントの Azure サブスクリプションを選択します。|
|Resource group|必須 |ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
|Automation アカウント名|必須 |場所とリソース グループに対して一意である名前を入力します。 削除された Automation アカウントの名前はすぐには使用できない場合があります。 一度ユーザー インターフェイスで入力されたアカウント名を変更することはできません。 |
|リージョン|必須 |ドロップダウン リストから、アカウントのリージョンを選びます。 Automation アカウントをデプロイできる場所の最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)」を参照してください。|

次の図は、新しい Automation アカウントの標準構成を示しています。

:::image type="content" source="./media/automation-create-standalone-account/create-account-basics.png" alt-text="[基本] タブで Automation アカウントを作成するために必要なフィールド":::

### <a name="advanced"></a>上級

**[詳細設定]** タブで、新しい Automation アカウントのマネージド ID オプションを構成できます。 Automation アカウントを作成した後で、ユーザー割り当てマネージド ID オプションを構成することもできます。

ユーザー割り当てマネージド ID を作成する方法については、「[ユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

次の表では、 **[詳細設定]** タブのフィールドを説明します。

| **フィールド** | **必須**<br> **or**<br> **省略可能** |**説明** |
|---|---|---|
|システム割り当て |オプション |Automation アカウントのライフサイクルに関連付けられている Azure Active Directory ID。 |
|ユーザー割り当て |オプション |使うリソースとは別に管理されるスタンドアロンの Azure リソースとして表されるマネージド ID。|

マネージド ID を有効にするかは後で選ぶことができ、Automation アカウントはそれなしで作成されます。 アカウントの作成後にマネージド ID を有効にするには、「[マネージド ID の有効化](enable-managed-identity-for-automation.md)」を参照してください。 両方のオプションを選ぶ場合、ユーザー割り当て ID については **[ユーザー割り当て ID の追加]** オプションを選びます。 **[ユーザー割り当て済みマネージド ID の選択]** ページで、サブスクリプションを選び、そのサブスクリプションで作成された 1 つ以上のユーザー割り当て ID を追加して、Automation アカウントに割り当てます。

次の図は、新しい Automation アカウントの標準構成を示しています。

:::image type="content" source="./media/automation-create-standalone-account/create-account-advanced.png" alt-text="[詳細設定] タブで Automation アカウントを作成するために必要なフィールド":::

### <a name="tags-tab"></a>[タグ] タブ

**[タグ]** タブでは、Azure リソースを整理するための Resource Manager タグを指定できます。 詳細については、「[論理的な組織化のためにリソース、リソース グループ、サブスクリプションにタグを付ける](../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認および作成]** タブに移動すると、選んだ Automation アカウントの設定の検証が Azure によって実行されます。 検証に成功した場合は、Automation アカウントの作成に進むことができます。

検証が失敗した場合は、変更する必要がある設定がポータルに示されます。

新しい Automation アカウントを確認します。

:::image type="content" source="./media/automation-create-standalone-account/automation-account-overview.png" alt-text="Automation アカウントの概要ページ":::

Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。 これらの作成後、Runbook を保持しない場合は安全に削除できます。 マネージド ID を使い、Runbook でアカウントに対する認証を行うことができます。別の実行アカウントを作成する場合や実行アカウントを必要としない場合を除き、実行アカウントは残しておく必要があります。 アカウントのリソースを次の表に示します。

|リソース |説明 | |------||------| |AzureAutomationTutorial Runbook |実行アカウントを使った認証の方法を示すサンプルのグラフィカルな Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。 | |AzureAutomationTutorialScript |実行アカウントを使った認証の方法を示す、サンプルの PowerShell Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。| |AzureAutomationTutorialPython2Runbook |実行アカウントを使った認証の方法を示す、サンプルの Python Runbook。 この Runbook には、サブスクリプション内に存在するすべてのリソース グループが一覧表示されます。|

> [!NOTE]
> チュートリアルの Runbook は、マネージド ID を使って認証するように更新されていません。 リソースへのアクセス権をマネージド ID に付与し、いずれかの種類のマネージド ID を使って認証を行う Runbook を構成する方法については、「[システム割り当て ID を使う](enable-managed-identity-for-automation.md#give-access-to-azure-resources-by-obtaining-a-token)」または「[ユーザー割り当て ID を使う](add-user-assigned-identity.md#give-identity-access-to-azure-resources-by-obtaining-a-token)」を参照してください。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](./learn/powershell-runbook-managed-identity.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[チュートリアル:PowerShell Workflow Runbook を作成する](learn/automation-tutorial-runbook-textual.md)」を参照してください。
* Python 3 Runbook の使用を開始するには、「[チュートリアル: Python 3 Runbook を作成する](learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。