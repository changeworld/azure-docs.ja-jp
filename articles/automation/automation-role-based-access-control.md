---
title: Azure Automation におけるロールベースのアクセス制御
description: Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。 この記事では、Azure Automation における RBAC の設定方法について説明します。
keywords: Automation RBAC, ロールベースのアクセス制御, Azure RBAC
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c9cdebd2fb7f650b042fa04f345ac440e0b41cb8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Azure Automation におけるロールベースのアクセス制御

Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。 [RBAC](../role-based-access-control/role-assignments-portal.md) を使用して、チーム内の職務を分離し、それぞれの職務に必要なアクセス権のみをユーザー、グループ、アプリケーションに付与することができます。 ロールベースのアクセス権をユーザーに付与するには、Azure Portal、Azure コマンドライン ツール、Azure Management API を使用します。

## <a name="roles-in-automation-accounts"></a>Automation アカウントのロール
Azure Automation でアクセス権を付与するには、Automation アカウント スコープのユーザー、グループ、アプリケーションに適切な RBAC ロールを割り当てます。 Automation アカウントでは次の組み込みロールがサポートされています。

| **ロール** | **説明** |
|:--- |:--- |
| Owner |Automation アカウント内のすべてのリソースおよびアクションへのアクセスは、所有者ロールによって許可されます。Automation アカウントを管理するためのアクセス権を他のユーザー、グループ、アプリケーションに付与することもできます。 |
| Contributor |Automation アカウントに対する他のユーザーのアクセス許可に変更を加えることを除くすべての作業は共同作成者ロールで行うことができます。 |
| Reader |閲覧者ロールでは、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。 |
| Automation Operator |ジョブの開始、停止、一時停止、再開、スケジュール設定などの運用タスクは、Automation オペレーター ロールで実行できます。 Automation アカウントのリソース (資格情報アセットや Runbook など) を閲覧したり改変したりできないよう保護したうえで、同じ組織のメンバーにのみ、それらの Runbook の実行を許可する必要がある場合、このロールを活用できます。 |
|Automation ジョブ オペレーター|Automation ジョブ オペレーター ロールでは、Automation Runbook を使用してジョブを作成および管理できます。|
|Automation Runbook オペレーター|Automation Runbook オペレーター ロールでは、Runbook のプロパティを読み取ることができます。 Runbook のジョブを作成することもできます。|
| Log Analytics 共同作成者 | Log Analytics 共同作成者ロールでは、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。|
| Log Analytics 閲覧者 | Log Analytics 閲覧者ロールでは、すべての監視データの表示と検索、および監視設定の表示を行うことができます。 これには、すべての Azure リソースに対する Azure 診断の構成の表示が含まれます。 |
| Monitoring Contributor | 共同作成者の監視ロールでは、すべての監視データを読み取り、監視設定を更新できます。|
| Monitoring Reader | 閲覧者の監視ロールでは、すべての監視データを読み取ることができます。 |
| User Access Administrator |Azure Automation アカウントに対するユーザー アクセスは、ユーザー アクセスの管理者ロールで管理できます。 |

## <a name="role-permissions"></a>ロールのアクセス許可

以降の表は、各ロールに割り当てられている具体的なアクセス許可の説明です。 アクセス許可を与える Actions のほか、それらを制限する NotActions が含まれている場合があります。

### <a name="owner"></a>Owner

所有者は、アクセス権を含めすべてを管理できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|アクション|[説明]|
|---|---|
|Microsoft.Automation/automationAccounts/|あらゆる種類のリソースの作成と管理。|

### <a name="contributor"></a>Contributor

共同作業者は、アクセス権以外のすべてを管理できます。 次の表は、このロールに付与および否認されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|あらゆる種類のリソースの作成と管理|
|**否認されるアクション**||
|Microsoft.Authorization/*/Delete| ロールとロール割り当ての削除。       |
|Microsoft.Authorization/*/Write     |  ロールとロール割り当ての作成。       |
|Microsoft.Authorization/elevateAccess/Action    | ユーザー アクセス管理者を作成する機能の拒否。       |

### <a name="reader"></a>Reader

閲覧者は、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Automation アカウントのすべてのリソースの表示。 |

### <a name="automation-job-operator"></a>Automation ジョブ オペレーター

Automation ジョブ オペレーターは、Automation アカウントのスコープで付与されます。 これにより、オペレーターのアクセス許可でアカウントのジョブを管理できます。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Authorization/*/read|承認の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook のジョブの一覧表示。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|一時停止されているジョブの再開。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|進行中のジョブの取り消し。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|ジョブ ストリームと出力の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|進行中のジョブの一時停止。|
|Microsoft.Automation/automationAccounts/jobs/write|ジョブの作成。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  ロールとロール割り当ての読み取り。       |
|Microsoft.Resources/deployments/*      |リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/* |サポート チケットの作成と管理。|

### <a name="automation-runbook-operator"></a>Automation Runbook オペレーター

Automation Runbook オペレーター ロールは、Runbook のスコープで付与されます。 Automation Runbook オペレーターは、Runbook 名を表示できます。 このアクセス許可を Automation アカウント スコープの "Automation ジョブ オペレーター" と組み合わせると、オペレーターは、特定の Runbook の Automation オペレーター アクションを実行できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Runbook の一覧表示。        |
|Microsoft.Authorization/*/read      | 承認の読み取り。        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |ロールとロール割り当ての読み取り。         |
|Microsoft.Resources/deployments/*      | リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/*      | サポート チケットの作成と管理。        |

### <a name="automation-operator"></a>Automation Operator

Automation オペレーターは、ジョブを開始、停止、中断、再開することができます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|Microsoft.Authorization/*/read|承認の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook のジョブの一覧表示。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|一時停止されているジョブの再開。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|進行中のジョブの取り消し。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|ジョブ ストリームと出力の読み取り。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|進行中のジョブの一時停止。|
|Microsoft.Automation/automationAccounts/jobs/write|ジョブの作成。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |ロールとロール割り当ての読み取り。         |
|Microsoft.Resources/deployments/*      |リソース グループ デプロイの作成と管理。         |
|Microsoft.Insights/alertRules/*      | アラート ルールの作成と管理。        |
|Microsoft.Support/* |サポート チケットの作成と管理。|

### <a name="log-analytics-contributor"></a>Log Analytics 共同作成者

Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.Automation/automationAccounts/*|Automation アカウントの管理。|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|仮想マシン拡張機能の作成と管理。|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|従来のストレージ アカウントの一覧表示。|
|Microsoft.Compute/virtualMachines/extensions/*|従来の仮想マシン拡張機能の作成と管理。|
|Microsoft.Insights/alertRules/*|アラート ルールの読み取り/書き込み/削除を実行します。|
|Microsoft.Insights/diagnosticSettings/*|診断設定の読み取り/書き込み/削除を実行します。|
|Microsoft.OperationalInsights/*|Log Analytics の管理。|
|Microsoft.OperationsManagement/*|ワークスペースでのソリューションの管理。|
|Microsoft.Resources/deployments/*|リソース グループ デプロイの作成と管理。|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|リソース グループ デプロイの作成と管理。|
|Microsoft.Storage/storageAccounts/listKeys/action|ストレージ アカウント キーの一覧表示。|
|Microsoft.Support/*|サポート チケットの作成と管理。|


### <a name="log-analytics-reader"></a>Log Analytics 閲覧者

Log Analytics 閲覧者は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure 診断構成の表示など、監視設定の表示を行うことができます。 次の表は、このロールに付与または否認されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Log Analytics でのクエリの管理。|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics データの検索。|
|Microsoft.Support/*|サポート チケットの作成と管理。|
|**否認されるアクション**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|共有アクセス キーを読み取ることはできません。|

### <a name="monitoring-contributor"></a>Monitoring Contributor

"共同作成者の監視" は、すべての監視データを読み取り、監視設定を更新できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.AlertsManagement/alerts/*|アラートの管理。|
|Microsoft.AlertsManagement/alertsSummary/*|アラート ダッシュボードの管理。|
|Microsoft.Insights/AlertRules/*|アラート ルールの管理。|
|Microsoft.Insights/components/*|Application Insights コンポーネントの管理。|
|Microsoft.Insights/DiagnosticSettings/*|診断設定の管理。|
|Microsoft.Insights/eventtypes/*|サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。|
|Microsoft.Insights/LogDefinitions/*|このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。|
|Microsoft.Insights/MetricDefinitions/*|メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。|
|Microsoft.Insights/Metrics/*|リソースのメトリックを読み取ります。|
|Microsoft.Insights/Register/Action|Microsoft Insights プロバイダーを登録します。|
|Microsoft.Insights/webtests/*|Application Insights の Web テストの管理。|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Log Analytics のソリューション パックの管理。|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Log Analytics の保存した検索条件の管理。|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics ワークスペースを検索します。|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics ワークスペースのキーを一覧表示します。|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Log Analytics ストレージ分析情報構成の管理。|
|Microsoft.Support/*|サポート チケットの作成と管理。|
|Microsoft.WorkloadMonitor/workloads/*|ワークロードの管理。|

### <a name="monitoring-reader"></a>Monitoring Reader

"閲覧者の監視" は、すべての監視データを読み取ることができます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|機密データを除くあらゆる種類のリソースの読み取り|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics ワークスペースを検索します。|
|Microsoft.Support/*|サポート チケットの作成と管理|

### <a name="user-access-administrator"></a>User Access Administrator

ユーザー アクセス管理者は、Azure リソースへのユーザー アクセスを管理できます。 次の表は、このロールに付与されるアクセス許可を示しています。

|**アクション**  |**説明**  |
|---------|---------|
|*/read|すべてのリソースの読み取り|
|Microsoft.Authorization/*|承認の管理|
|Microsoft.Support/*|サポート チケットの作成と管理|

## <a name="onboarding"></a>オンボード

変更追跡または更新管理ソリューション用の仮想マシンのオンボードに必要な最小限のアクセス許可を次の表に示します。

### <a name="onboarding-from-a-virtual-machine"></a>仮想マシンからのオンボード

|**アクション**  |**アクセス許可**  |**最小スコープ**  |
|---------|---------|---------|
|新しいデプロイを記述する      | Microsoft.Resources/deployments/*          |[サブスクリプション]          |
|新しいリソース グループを記述する      | Microsoft.Resources/subscriptions/resourceGroups/write        | [サブスクリプション]          |
|新しい既定のワークスペースを作成する      | Microsoft.OperationalInsights/workspaces/write         | リソース グループ         |
|新しいアカウントを作成する      |  Microsoft.Automation/automationAccounts/write        |リソース グループ         |
|ワークスペースとアカウントをリンクする      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|ワークスペース</br>Automation アカウント
|ソリューションを作成する      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |リソース グループ          |
|MMA 拡張機能を作成する      | Microsoft.Compute/virtualMachines/write         | 仮想マシン         |
|保存した検索条件を作成する      | Microsoft.OperationalInsights/workspaces/write          | ワークスペース         |
|スコープ構成を作成する      | Microsoft.OperationalInsights/workspaces/write          | ワークスペース         |
|ソリューションをスコープ構成にリンクする      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | 解決策         |
|オンボード状態の確認 - ワークスペースを読み取る      | Microsoft.OperationalInsights/workspaces/read         | ワークスペース         |
|オンボード状態の確認 - アカウントのリンクされたワークスペースのプロパティを読み取る     | Microsoft.Automation/automationAccounts/read      | Automation アカウント        |
|オンボード状態の確認 - ソリューションを読み取る      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 解決策         |
|オンボード状態の確認 - VM を読み取る      | Microsoft.Compute/virtualMachines/read         | 仮想マシン         |
|オンボード状態の確認 - アカウントを読み取る      | Microsoft.Automation/automationAccounts/read  |  Automation アカウント   |

### <a name="onboarding-from-automation-account"></a>Automation アカウントからのオンボード

|**アクション**  |**アクセス許可** |**最小スコープ**  |
|---------|---------|---------|
|新しいデプロイを作成する     | Microsoft.Resources/deployments/*        | [サブスクリプション]         |
|新しいリソース グループの作成     | Microsoft.Resources/subscriptions/resourceGroups/write         | [サブスクリプション]        |
|AutomationOnboarding ブレード - 新しいワークスペースを作成する     |Microsoft.OperationalInsights/workspaces/write           | リソース グループ        |
|AutomationOnboarding ブレード - リンクされたワークスペースを読み取る     | Microsoft.Automation/automationAccounts/read        | Automation アカウント       |
|AutomationOnboarding ブレード - ソリューションを読み取る     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 解決策        |
|AutomationOnboarding ブレード - ワークスペースを読み取る     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | ワークスペース        |
|ワークスペースとアカウントのリンクを作成する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
|Shoebox のアカウントを記述する      | Microsoft.Automation/automationAccounts/write        | アカウント        |
|ソリューションを作成する      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | リソース グループ         |
|保存した検索条件を作成および編集する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
|スコープ構成を作成および編集する     | Microsoft.OperationalInsights/workspaces/write        | ワークスペース        |
|ソリューションをスコープ構成にリンクする      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | 解決策         |
|**手順 2 - 複数の VM のオンボード**     |         |         |
|VMOnboarding ブレード - MMA 拡張機能を作成する     | Microsoft.Compute/virtualMachines/write           | 仮想マシン        |
|保存した検索条件を作成および編集する     | Microsoft.OperationalInsights/workspaces/write           | ワークスペース        |
|スコープ構成を作成および編集する  | Microsoft.OperationalInsights/workspaces/write   | ワークスペース|

## <a name="update-management"></a>更新管理

更新管理は、複数のサービスにまたがってサービスを提供します。 更新管理デプロイを管理するために必要なアクセス許可を次の表に示します。

|**リソース**  |**ロール**  |**スコープ**  |
|---------|---------|---------|
|Automation アカウント     | Log Analytics 共同作成者       | Automation アカウント        |
|Automation アカウント    | Virtual Machine Contributor        | アカウントのリソース グループ        |
|Log Analytics ワークスペース     | Log Analytics 共同作成者| Log Analytics ワークスペース        |
|Log Analytics ワークスペース |Log Analytics 閲覧者| [サブスクリプション]|
|解決策     |Log Analytics 共同作成者         | 解決策|
|仮想マシン     | Virtual Machine Contributor        | 仮想マシン        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Azure Portal を使用して Automation アカウントの RBAC を構成する
1. [Azure Portal](https://portal.azure.com/) にログインし、[Automation アカウント] ページから、ご利用の Automation アカウントを開きます。  
2. 左上隅にある **[アクセス制御 (IAM)]** コントロールをクリックします。 表示された **[アクセス制御 (IAM)]** ページでは、ご利用の Automation アカウントを管理するための新しいユーザー、グループ、アプリケーションを追加できるほか、その Automation アカウント用に構成できる既存のロールを確認できます。
   
   ![Access button](media/automation-role-based-access-control/automation-01-access-button.png)  

### <a name="add-a-new-user-and-assign-a-role"></a>新しいユーザーの追加とロールの割り当て
1. **[アクセス制御 (IAM)]** ページの **[+ 追加]** をクリックして **[アクセス許可の追加]** ページを開きます。このページでは、ユーザー、グループ、アプリケーションを追加し、それらにロールを割り当てることができます。  

2. 利用可能なロールの一覧からロールを選択します。 Automation アカウントでサポートされている任意の組み込みロールを選択してもかまいません。また、自分で定義したカスタム ロールを選択することもできます。

3. アクセス許可を付与するユーザーのユーザー名を **[選択]** フィールドに入力します。 一覧からユーザーを選択し、**[保存]** をクリックします。
   
   ![Add users](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   **[ユーザー]** ページに目的のユーザーが追加され、選択したロールが割り当てられていることを確認できます。  
   
   ![List users](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   **[ロール]** ページから、ユーザーにロールを割り当てることもできます。 
4. **[アクセス制御 (IAM)]** ページから **[ロール]** をクリックして **[ロール]** ページを開きます。 ここでは、ロールの名前と、そのロールに割り当てられているユーザー数およびグループ数を確認できます。
   
    ![[ユーザー] ページからのロールの割り当て](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > ロールベースのアクセス制御は、Automation アカウント スコープでのみ設定できます。Automation アカウントより下のリソース レベルで設定することはできません。

### <a name="remove-a-user"></a>ユーザーの削除
Automation アカウントの管理に関与しないユーザーや既に退社したユーザーについては、アクセス権を削除することができます。 ユーザーを削除する手順を次に示します。 

1. **[アクセス制御 (IAM)]** ページで、削除するユーザーを選択し、**[削除]** をクリックします。
2. 割り当ての詳細ウィンドウで、**[削除]** ボタンをクリックします。
3. **[はい]** をクリックして削除を確定します。

   ![Remove users](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>ロールが割り当てられているユーザー

ロールに割り当てられているユーザーが Azure にログインし、その Automation アカウントを選択すると、**[ディレクトリ]**の一覧に所有者のアカウントが表示されていることが確認できます。 自分が追加されている Automation アカウントを確認するには、既定のディレクトリを所有者の既定のディレクトリに切り替える必要があります。

### <a name="user-experience-for-automation-operator-role"></a>Automation オペレーター ロールのユーザー エクスペリエンス
Automation オペレーター ロールに割り当てられているユーザーが、その割り当て先となっている Automation アカウントを表示した場合、実際に表示されるのはその Automation アカウントで作成された Runbook、Runbook ジョブ、およびスケジュールの一覧のみで、それらの定義は表示されません。 これらのユーザーは、Runbook ジョブの開始、停止、一時停止、再開、スケジュール設定を実行することができます。 Automation の構成、ハイブリッド worker グループ、DSC ノードなど、他のリソースにアクセスすることはできません。

![リソースへのアクセス権がない](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

スケジュールを表示および作成することはできますが、その他の種類のアセットは利用できません。

Runbook に関連付けられている Webhook を表示するアクセス権もこのユーザーにはありません。

![No access to webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Azure PowerShell を使用して Automation アカウントの RBAC を構成する
Automation アカウントに対するロールベースのアクセス制御は、次の [Azure PowerShell コマンドレット](../role-based-access-control/role-assignments-powershell.md)を使用して構成することもできます。

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) : Azure Active Directory で利用できるすべての RBAC ロールを一覧表示します。 このコマンドに **Name** プロパティを組み合わせることで、特定のロールで実行できるすべてのアクションを一覧表示できます。

```powershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

出力の例を次に示します。

```powershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action, 
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
``` 

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) : 特定のスコープにおける Azure AD RBAC ロールの割り当てを一覧表示します。 このコマンドにパラメーターを指定しなかった場合、対象サブスクリプションで行われたすべてのロールの割り当てが返されます。 指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを一覧表示するには、 **ExpandPrincipalGroups** パラメーターを使用します。  
    **例:** Automation アカウント内のすべてのユーザーとそのロールを一覧表示するには、次のコマンドを使用します。

```powershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

出力の例を次に示します。

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx): 特定のスコープのユーザー、グループ、アプリケーションにアクセス権を割り当てます。  
    **例:** Automation アカウント スコープのユーザーに対して "Automation オペレーター" ロールを割り当てるには、次のコマンドを使用します。

```powershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

出力の例を次に示します。

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

• [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx): 特定のスコープの指定したユーザー、グループ、またはアプリケーションのアクセス権を削除します。  
    **例:** Automation アカウント スコープの "Automation オペレーター" ロールからユーザーを削除するには、次のコマンドを使用します。

```powershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

前の例での**サインイン ID**、**サブスクリプション ID**、**リソース グループ名**、**Automation アカウント名**は、実際のアカウント情報に置き換えてください。 ユーザー ロールの割り当ての削除を続行する前に確認を求められた場合は、 **[はい]** を選択します。   

## <a name="next-steps"></a>次の手順
* Azure Automation の RBAC を構成する各種の方法については、 [Azure PowerShell を使用した RBAC の管理](../role-based-access-control/role-assignments-powershell.md)に関する記事を参照してください。
* Runbook を開始するさまざまな方法については、 [Runbook の開始](automation-starting-a-runbook.md)
* Runbook のさまざまな種類については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)

