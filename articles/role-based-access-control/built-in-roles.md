---
title: Azure ロールベースのアクセス制御 (RBAC) の組み込みロール | Microsoft Docs
description: このトピックでは、ロール ベースのアクセス制御 (RBAC) の組み込みのロールについて説明します。 ロールは継続的に追加されるので、ドキュメントが最新かどうか確認してください。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 4e413d921390cfb87c27c530c6870945c28c0df8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御の組み込みロール
Azure のロールベースのアクセス制御 (RBAC) には、ユーザー、グループ、サービスに割り当てられる次の組み込みのロールが用意されています。 組み込みのロールの定義は変更できません。 ただし、組織の具体的なニーズに合うように [Azure RBAC のカスタム ロール](custom-roles.md) を作成することができます。

## <a name="built-in-role-descriptions"></a>組み込みのロールの説明
次の表に、組み込みのロールについての簡単な説明を示します。 ロール名をクリックすると、そのロールの **actions** と **notactions** の詳細な一覧を確認できます。 **actions** プロパティは、Azure リソースに対して許可するアクションを指定します。 アクションの文字列にワイルドカード文字を使用できます。 **notactions** プロパティは、許可するアクションから除外されるアクションを指定します。

アクションは、指定したリソースの種類で実行できる操作の種類を定義します。 例: 
- **Write** を使用すると、PUT、POST、PATCH、および DELETE 操作を実行できます。
- **Read** を使用すると、GET 操作を実行できます。

この記事では、現在存在するさまざまなロールについてのみ説明します。 ユーザーにロールを割り当てるとき、許可されているアクションをさらを制限するには、スコープを定義します。 これは、1 つのリソース グループについてのみ、あるユーザーを Web サイトの共同作業者として指定する場合に便利です。

> [!NOTE]
> Azure のロール定義は常に進化しています。 この記事は、最新の状態であることを心掛けておりますが、Azure PowerShell で常に最新のロール定義を見つけることができます。 現在のロールの一覧を表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドレットを使用します。 特定のロールの詳細を確認するには、適宜 `(get-azurermroledefinition "<role name>").actions` または `(get-azurermroledefinition "<role name>").notactions` を使用します。 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) を使用すると、特定の Azure リソース プロバイダーの操作が一覧表示されます。


| 組み込みのロール | [説明] |
| --- | --- |
| [Owner](#owner) | リソースへのアクセスを含め、すべてを管理できます。 |
| [Contributor](#contributor) | リソースへのアクセス以外のすべてを管理できます。 |
| [Reader](#reader) | すべてを表示できますが、変更することはできません。 |
| [API Management Service Contributor](#api-management-service-contributor) | サービスと API を管理できます |
| [API Management Service Operator Role](#api-management-service-operator-role) | サービスを管理できますが、API は対象外です |
| [API Management Service Reader Role](#api-management-service-reader-role) | サービスと API への読み取り専用アクセスです |
| [Application Insights Component Contributor](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger 機能を使用するアクセス許可をユーザーに付与します |
| [Automation Job Operator](#automation-job-operator) | Automation Runbook を使用してジョブを作成および管理します。 |
| [Automation Operator](#automation-operator) | Automation オペレーターはジョブを開始、停止、中断、再開することができます |
| [Automation Runbook Operator](#automation-runbook-operator) | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 |
| [Azure Stack Registration Owner](#azure-stack-registration-owner) | Azure Stack の登録を管理できます。 |
| [Backup Contributor](#backup-contributor) | バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません |
| [Backup Operator](#backup-operator) | バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く) |
| [Backup Reader](#backup-reader) | バックアップ サービスを表示できますが、変更を行うことはできません |
| [Billing Reader](#billing-reader) | 課金データへの読み取りアクセスを許可します |
| [BizTalk Contributor](#biztalk-contributor) | BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。 |
| [CDN Endpoint Contributor](#cdn-endpoint-contributor) | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Endpoint Reader](#cdn-endpoint-reader) | CDN エンドポイントを表示できますが、変更はできません。 |
| [CDN Profile Contributor](#cdn-profile-contributor) | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Profile Reader](#cdn-profile-reader) | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 |
| [Classic Network Contributor](#classic-network-contributor) | 従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Classic Storage Account Contributor](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できますが、アクセスすることはできません。 |
| [従来のストレージ アカウント キー オペレーターのサービス ロール](#classic-storage-account-key-operator-service-role) | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | 従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
| [ClearDB MySQL DB Contributor](#cleardb-mysql-db-contributor) | ClearDB MySQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Cosmos DB アカウントの閲覧者ロール](#cosmos-db-account-reader-role) | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 |
| [Data Factory Contributor](#data-factory-contributor) | データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。 |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 |
| [DevTest Labs User](#devtest-labs-user) | Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。 |
| [DNS Zone Contributor](#dns-zone-contributor) | Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
| [DocumentDB Account Contributor](#documentdb-account-contributor) | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 |
| [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Key Vault Contributor](#key-vault-contributor) | キー コンテナーを管理できますが、アクセスすることはできません。 |
| [Lab Creator](#lab-creator) | Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。 |
| [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。 |
| [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure 診断構成の表示など、監視設定の表示を行うことができます。 |
| [Logic App Contributor](#logic-app-contributor) | ロジック アプリを管理できますが、アクセスすることはできません。 |
| [Logic App Operator](#logic-app-operator) | ロジック アプリの読み取り、有効化、無効化ができます。 |
| [Managed Identity Contributor](#managed-identity-contributor) | ユーザー割り当て ID の作成、読み取り、更新、削除を行います |
| [Managed Identity Operator](#managed-identity-operator) | ユーザー割り当て ID の読み取りと割り当てを行います |
| [Monitoring Contributor](#monitoring-contributor) | すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
| [Monitoring Reader](#monitoring-reader) | すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
| [Network Contributor](#network-contributor) | ネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
| [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Redis Cache Contributor](#redis-cache-contributor) | Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。 |
| [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Search Service Contributor](#search-service-contributor) | Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Security Admin](#security-admin) | Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます |
| [Security Manager](#security-manager) | セキュリティ コンポーネント、セキュリティ ポリシー、Virtual Machines を管理できます |
| [Security Reader](#security-reader) | Security Center のみ: 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません |
| [Site Recovery Contributor](#site-recovery-contributor) | 資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます |
| [Site Recovery Operator](#site-recovery-operator) | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません |
| [Site Recovery Reader](#site-recovery-reader) | Site Recovery の状態を表示できますが、その他の管理操作は実行できません |
| [SQL DB Contributor](#sql-db-contributor) | SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 |
| [SQL Security Manager](#sql-security-manager) | SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 |
| [SQL Server Contributor](#sql-server-contributor) | SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 |
| [Storage Account Contributor](#storage-account-contributor) | ストレージ アカウントを管理できますが、アクセスすることはできません。 |
| [ストレージ アカウント キー オペレーターのサービス ロール](#storage-account-key-operator-service-role) | ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [Support Request Contributor](#support-request-contributor) | Support request を作成して管理できます |
| [Traffic Manager Contributor](#traffic-manager-contributor) | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
| [User Access Administrator](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理します。 |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | このロールが割り当てられたユーザーは、Windows 管理者または Linux の root ユーザー特権で仮想マシンにログインできます。 |
| [Virtual Machine Contributor](#virtual-machine-contributor) | 仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
| [Virtual Machine User Login](#virtual-machine-user-login) | このロールが割り当てられたユーザーは、通常のユーザーとして仮想マシンにログインできます。 |
| [Web Plan Contributor](#web-plan-contributor) | Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Website Contributor](#website-contributor) | Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。 |

以降の表は、各ロールに割り当てられている具体的なアクセス許可の説明です。 アクセス許可を与える **Actions** のほか、それらを制限する **NotActions** が含まれている場合があります。

## <a name="owner"></a>Owner
リソースへのアクセスを含め、すべてを管理できます。

| **アクション** |  |
| --- | --- |
| * | あらゆる種類のリソースの作成と管理 |

## <a name="contributor"></a>Contributor
リソースへのアクセス以外のすべてを管理できます。

| **アクション** |  |
| --- | --- |
| * | あらゆる種類のリソースの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | ロールとロール割り当ては削除できません |
| Microsoft.Authorization/*/Write | ロールとロール割り当ては作成できません |
| Microsoft.Authorization/elevateAccess/Action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |

## <a name="reader"></a>Reader
すべてを表示できますが、変更することはできません。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |

## <a name="api-management-service-contributor"></a>API Management Service Contributor
サービスと API を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | API Management サービスの作成と管理 |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="api-management-service-operator-role"></a>API Management Service Operator Role
サービスを管理できますが、API は対象外です

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
| Microsoft.ApiManagement/service/delete | API Management サービス インスタンスを削除します。 |
| Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
| Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
| Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの SSL 証明書をアップロードします。 |
| Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
| Microsoft.ApiManagement/service/write | API Management サービスの新しいインスタンスの作成 |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |

## <a name="api-management-service-reader-role"></a>API Management Service Reader Role
サービスと API への読み取り専用アクセスです

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |

## <a name="application-insights-component-contributor"></a>Application Insights Component Contributor
Application Insights コンポーネントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.Insights/webtests/* | Web テストの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
Application Insights Snapshot Debugger 機能を使用するアクセス許可をユーザーに付与します

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-job-operator"></a>Automation ジョブ オペレーター
Automation Runbook を使用してジョブを作成および管理します。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
| Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-operator"></a>Automation Operator
Automation オペレーターはジョブを開始、停止、中断、再開することができます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
| Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
| Microsoft.Automation/automationAccounts/read | Azure Automation アカウントを取得します。 |
| Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
| Microsoft.Automation/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
| Microsoft.Automation/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-runbook-operator"></a>Automation Runbook オペレーター
Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="azure-stack-registration-owner"></a>Azure Stack Registration Owner
Azure Stack の登録を管理できます。

| **アクション** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace の製品の拡張詳細を取得します |
| Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
| Microsoft.AzureStack/registrations/read | Azure Stack の登録のプロパティを取得します |

## <a name="backup-contributor"></a>Backup Contributor
バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | バックアップの管理に関連するメタ データの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="backup-operator"></a>Backup Operator
バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く)

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | バックアップ保護項目を作成します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | ジョブを取り消します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Recovery Services コンテナーのバックアップ管理メタデータを返します。 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | すべての保護可能な項目の一覧を返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="backup-reader"></a>Backup Reader
バックアップ サービスを表示できますが、変更を行うことはできません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Recovery Services コンテナーのバックアップ管理メタデータを返します。 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |

## <a name="billing-reader"></a>Billing Reader
課金データへの読み取りアクセスを許可します

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Billing/*/read | 課金情報の読み取り |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="biztalk-contributor"></a>BizTalk Contributor
BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-endpoint-contributor"></a>CDN Endpoint Contributor
CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-endpoint-reader"></a>CDN Endpoint Reader
CDN エンドポイントを表示できますが、変更はできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-profile-contributor"></a>CDN Profile Contributor
CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-profile-reader"></a>CDN Profile Reader
CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-network-contributor"></a>Classic Network Contributor
従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicNetwork/* | 従来のネットワークの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-contributor"></a>Classic Storage Account Contributor
従来のストレージ アカウントを管理できますが、アクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicStorage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-key-operator-service-role"></a>従来のストレージ アカウント キー オペレーターのサービス ロール
従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます

| **アクション** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |

## <a name="classic-virtual-machine-contributor"></a>Classic Virtual Machine Contributor
従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicCompute/domainNames/* | 従来のコンピューティング ドメイン名の作成と管理 |
| Microsoft.ClassicCompute/virtualMachines/* | 仮想マシンの作成と管理 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP をリンクします。 |
| Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP を取得します。 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークを取得します。 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
| Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージを返します。 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
| Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB Contributor
ClearDB MySQL データベースを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| successbricks.cleardb/databases/* | ClearDB MySQL データベースの作成と管理 |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB アカウントの閲覧者ロール
Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り (各ユーザーに付与されたアクセス許可を読み取ることができます) |
| Microsoft.DocumentDB/*/read | 任意のコレクションの読み取り |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
| Microsoft.Insights/MetricDefinitions/read | メトリック定義を読み取ります。 |
| Microsoft.Insights/Metrics/read | メトリックを読み取ります。 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="data-factory-contributor"></a>Data Factory Contributor
データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DataFactory/factory/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
| Microsoft.DataFactory/dataFactories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer
独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics アカウントを削除します。 |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
| Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics アカウントを作成または更新します。 |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | ファイアウォール規則を作成または更新します。 |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | ファイアウォール規則を削除します。 |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | コンピューティング ポリシーを作成または更新します。 |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | コンピューティング ポリシーを削除します。 |

## <a name="devtest-labs-user"></a>DevTest Labs User
Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティを取得します。 |
| Microsoft.Compute/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
| Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
| Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
| Microsoft.Compute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
| Microsoft.Compute/virtualMachines/start/action | 仮想マシンを起動します。 |
| Microsoft.DevTestLab/*/read | ラボのプロパティの読み取り |
| Microsoft.DevTestLab/labs/createEnvironment/action | ラボで仮想マシンを作成します。 |
| Microsoft.DevTestLab/labs/claimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
| Microsoft.DevTestLab/labs/formulas/delete | 数式を削除します。 |
| Microsoft.DevTestLab/labs/formulas/read | 数式を読み取ります。 |
| Microsoft.DevTestLab/labs/formulas/write | 数式を追加または変更します。 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | ラボ ポリシーを評価します。 |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | 既存の仮想マシンの所有権を取得します。 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則を接続します。 |
| Microsoft.Network/networkInterfaces/*/read | ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
| Microsoft.Network/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 |
| Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
| Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
| Microsoft.Network/publicIPAddresses/*/read | パブリック IP アドレスのプロパティの読み取り |
| Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 |
| Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 |
| Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
| Microsoft.Resources/deployments/read | デプロイを取得または一覧表示します。 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |

## <a name="dns-zone-contributor"></a>DNS Zone Contributor
Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/dnsZones/* | DNS ゾーンとレコードの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor
Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems Account Contributor
Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="key-vault-contributor"></a>Key Vault Contributor
キー コンテナーを管理できますが、アクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Lab Creator
Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.ManagedLab/labAccounts/createLab/action | ラボ アカウントにラボを作成します。 |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="log-analytics-contributor"></a>Log Analytics 共同作成者
Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="log-analytics-reader"></a>Log Analytics 閲覧者
Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure 診断構成の表示など、監視設定の表示を行うことができます。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
| Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |

## <a name="logic-app-contributor"></a>Logic App Contributor
ロジック アプリを管理できますが、アクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
| Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
| Microsoft.Insights/logdefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
| Microsoft.Insights/metricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
| Microsoft.Logic/* | Logic Apps リソースを管理します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/connectionGateways/* | 接続ゲートウェイを作成および管理します。 |
| Microsoft.Web/connections/* | 接続を作成および管理します。 |
| Microsoft.Web/customApis/* | カスタム API を作成および管理します。 |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
| Microsoft.Web/sites/functions/listSecrets/action | Web アプリの関数のシークレットを一覧表示します。 |

## <a name="logic-app-operator"></a>Logic App Operator
ロジック アプリの読み取り、有効化、無効化ができます。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/*/read | Insights のアラート ルールを読み取ります |
| Microsoft.Insights/diagnosticSettings/*/read | Logic Apps の診断設定を取得します |
| Microsoft.Insights/metricDefinitions/*/read | Logic Apps の利用可能なメトリックを取得します。 |
| Microsoft.Logic/*/read | Logic Apps リソースを読み取ります。 |
| Microsoft.Logic/workflows/disable/action | ワークフローを無効にします。 |
| Microsoft.Logic/workflows/enable/action | ワークフローを有効にします。 |
| Microsoft.Logic/workflows/validate/action | ワークフローを検証します。 |
| Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
| Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/connectionGateways/*/read | 接続ゲートウェイを読み取ります。 |
| Microsoft.Web/connections/*/read | 接続を読み取ります。 |
| Microsoft.Web/customApis/*/read | カスタム API を読み取ります。 |
| Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |

## <a name="managed-identity-contributor"></a>Managed Identity Contributor
ユーザー割り当て ID の作成、読み取り、更新、削除を行います

| **アクション** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="managed-identity-operator"></a>Managed Identity Operator
ユーザー割り当て ID の読み取りと割り当てを行います

| **アクション** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="monitoring-contributor"></a>Monitoring Contributor
すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | アラート ルールの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/components/* | Application Insights コンポーネントの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/DiagnosticSettings/* | 診断設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/eventtypes/* | サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
| Microsoft.Insights/LogDefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
| Microsoft.Insights/MetricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
| Microsoft.Insights/Metrics/* | リソースのメトリックを読み取ります。 |
| Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
| Microsoft.Insights/webtests/* | Application Insights の Web テストの読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics ソリューション パックの読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Log Analytics によって保存された検索の読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Monitoring Reader
すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="network-contributor"></a>Network Contributor
ネットワークを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/* | ネットワークの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM Account Contributor
New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Redis Cache Contributor
Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cache/redis/* | Redis キャッシュの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collections Contributor
スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Scheduler/jobcollections/* | ジョブ コレクションの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="search-service-contributor"></a>Search Service Contributor
Search サービスを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Search/searchServices/* | 検索サービスの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-admin"></a>セキュリティ管理者
Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Authorization/policyAssignments/* | ポリシーの割り当ての作成と管理 |
| Microsoft.Authorization/policyDefinitions/* | ポリシー定義の作成と管理 |
| Microsoft.Authorization/policySetDefinitions/* | ポリシー セットの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |
| Microsoft.Security/locations/alerts/dismiss/action | セキュリティ アラートを無視します |
| Microsoft.Security/locations/tasks/dismiss/action | セキュリティ推奨事項を無視します。 |
| Microsoft.Security/policies/write | セキュリティ ポリシーを更新します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-manager"></a>Security Manager
セキュリティ コンポーネント、セキュリティ ポリシー、Virtual Machines を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.ClassicCompute/*/read | 従来の仮想マシンの構成情報の読み取り |
| Microsoft.ClassicCompute/virtualMachines/*/write | 従来の仮想マシンの構成の書き込み |
| Microsoft.ClassicNetwork/*/read | 従来のネットワークに関する構成情報の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Security/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-reader"></a>セキュリティ リーダー
Security Center のみ: 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません

| **アクション** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |

## <a name="site-recovery-contributor"></a>Site Recovery Contributor
資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-operator"></a>Site Recovery Operator
フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 保護コンテナーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | フェールオーバー |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | ストレージの分類を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | ジョブを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-reader"></a>Site Recovery Reader
Site Recovery の状態を表示できますが、その他の管理操作は実行できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 保護コンテナーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | ストレージの分類を読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | ジョブを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | ジョブを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります。 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-db-contributor"></a>SQL DB Contributor
SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | SQL データベースの作成と管理 |
| Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 監査ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/auditingSettings/* | 監査設定を編集することはできません |
| Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 接続ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | データ マスク ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | セキュリティの警告のポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/securityMetrics/* | セキュリティ基準を編集することはできません |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL Security Manager
SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Microsoft 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードの読み取り |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
| Microsoft.Sql/servers/databases/schemas/read | データベースのスキーマの一覧を取得します。 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | テーブルの列の一覧を取得します。 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | データベースのテーブルの一覧を取得します。 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-server-contributor"></a>SQL Server Contributor
SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | SQL サーバーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーは編集できません |
| Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定は編集できません |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーは編集できません |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定は編集できません |
| Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードを読み取ることはできません |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーは編集できません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーは編集できません |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーは編集できません |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準は編集できません |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーは編集できません |

## <a name="storage-account-contributor"></a>Storage Account Contributor
ストレージ アカウントを管理できますが、アクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | あらゆる承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* | 診断設定の管理 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="storage-account-key-operator-service-role"></a>ストレージ アカウント キー オペレーターのサービス ロール
ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます

| **アクション** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
| Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |

## <a name="support-request-contributor"></a>Support Request Contributor
Support request を作成して管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="traffic-manager-contributor"></a>Traffic Manager Contributor
Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="user-access-administrator"></a>User Access Administrator
Azure リソースに対するユーザー アクセスを管理します。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.Authorization/* | 承認の管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login
-   このロールが割り当てられたユーザーは、Windows 管理者または Linux の root ユーザー特権で仮想マシンにログインできます。

| **アクション** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Virtual Machine Contributor
仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Compute/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
| Microsoft.Compute/locations/* | コンピューティングの場所の作成と管理 |
| Microsoft.Compute/virtualMachines/* | 仮想マシンの作成と管理 |
| Microsoft.Compute/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則を接続します。 |
| Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 |
| Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
| Microsoft.Network/locations/* | ネットワークの場所の作成と管理 |
| Microsoft.Network/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
| Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 |
| Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
| Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 |
| Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | バックアップ保護項目を作成します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
| Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
| Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="virtual-machine-user-login"></a>Virtual Machine User Login
このロールが割り当てられたユーザーは、通常のユーザーとして仮想マシンにログインできます。

| **アクション** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Web Plan Contributor
Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/serverFarms/* | サーバー ファームの作成と管理 |

## <a name="website-contributor"></a>Website Contributor
Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/certificates/* | Web サイト証明書の作成と管理 |
| Microsoft.Web/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの名前を取得します。 |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
| Microsoft.Web/sites/* | Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |

## <a name="see-also"></a>関連項目
* [ロールベースのアクセス制御](role-assignments-portal.md): Azure Portal での RBAC の基本について説明します。
* [Azure RBAC のカスタム ロール](custom-roles.md): アクセスのニーズに合わせてカスタム ロールを作成する方法について説明します。
* [アクセス変更履歴レポートの作成](change-history-report.md): RBAC でのロール割り当ての変更を追跡します。
* [ロールベースのアクセス制御のトラブルシューティング](troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
* [Azure Security Center におけるアクセス許可](../security-center/security-center-permissions.md)
