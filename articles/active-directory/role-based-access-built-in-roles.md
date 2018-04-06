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
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御の組み込みロール
Azure のロールベースのアクセス制御 (RBAC) には、ユーザー、グループ、サービスに割り当てられる次の組み込みのロールが用意されています。 組み込みのロールの定義は変更できません。 ただし、組織の具体的なニーズに合うように [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md) を作成することができます。

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
| [Owner](#owner) | アクセス権を含めすべてを管理できます |
| [Contributor](#contributor) | アクセス権以外のすべてを管理できます。 |
| [Reader](#reader) | すべてを閲覧できますが、変更を加えることはできません |
| [API Management Service Contributor](#api-management-service-contributor) | API Management サービスを管理できます |
| [API Management Service Operator Role](#api-management-service-operator-role) | API Management サービスを管理できます |
| [API Management Service Reader Role](#api-management-service-reader-role) | API Management サービスを管理できます |
| [Application Insights コンポーネントの共同作業者](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger 機能を使用するアクセス許可をユーザーに付与します |
| [Automation Job Operator](#automation-job-operator) | Automation Runbook を使用してジョブを作成および管理します。 |
| [Automation Operator](#automation-operator) | ジョブを開始、停止、中断、および再開できます |
| [Automation Runbook Operator](#automation-runbook-operator) | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 |
| [Azure Stack Registration Owner](#azure-stack-registration-owner) | Azure Stack の登録を管理できます。 |
| [Backup Contributor](#backup-contributor) | Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます |
| [Backup Operator](#backup-operator) | コンテナーの作成、バックアップの削除、および他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます |
| [Backup Reader](#backup-reader) | Recovery Services コンテナーでのバックアップ管理を監視できます |
| [Billing Reader](#billing-reader) | すべての課金情報を見ることができます |
| [BizTalk Contributor](#biztalk-contributor) | BizTalk Services を管理できます |
| [CDN Endpoint Contributor](#cdn-endpoint-contributor) | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Endpoint Reader](#cdn-endpoint-reader) | CDN エンドポイントを表示できますが、変更はできません。 |
| [CDN Profile Contributor](#cdn-profile-contributor) | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Profile Reader](#cdn-profile-reader) | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 |
| [Classic Network Contributor](#classic-network-contributor) | 従来の仮想ネットワークと予約済み IP を管理できます |
| [Classic Storage Account Contributor](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できます |
| [従来のストレージ アカウント キー オペレーターのサービス ロール](#classic-storage-account-key-operator-service-role) | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | 接続している仮想ネットワークやストレージ アカウント以外の従来の仮想マシンを管理できます |
| [ClearDB MySQL DB Contributor](#cleardb-mysql-db-contributor) | ClearDB MySQL データベースを管理できます |
| [Cosmos DB アカウントの閲覧者ロール](#cosmos-db-account-reader-role) | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 |
| [Data Factory Contributor](#data-factory-contributor) | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 |
| [DevTest Labs User](#devtest-labs-user) | すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます |
| [DNS Zone Contributor](#dns-zone-contributor) | DNS ゾーンとレコードを保護できます。 |
| [DocumentDB Account Contributor](#documentdb-account-contributor) | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 |
| [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Intelligent Systems アカウントを管理できます |
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
| [Network Contributor](#network-contributor) | すべてのネットワーク リソースを管理できます |
| [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Redis Cache Contributor](#redis-cache-contributor) | Redis キャッシュを管理できます |
| [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | Scheduler Job Collection を管理できます |
| [Search Service Contributor](#search-service-contributor) | Search サービスを管理できます |
| [Security Admin](#security-admin) | Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます |
| [Security Manager](#security-manager) | セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます |
| [Security Reader](#security-reader) | Security Center のみ: 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません |
| [Site Recovery Contributor](#site-recovery-contributor) | Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべての Site Recovery 管理アクションを管理できます |
| [Site Recovery Operator](#site-recovery-operator) | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作の実行や、他のユーザーへのアクセス権の割り当てはできません |
| [Site Recovery Reader](#site-recovery-reader) | Recovery Services コンテナーで Site Recovery の状態を監視し、サポート チケットを発生させることができます |
| [SQL DB Contributor](#sql-db-contributor) | SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [SQL Security Manager](#sql-security-manager) | SQL サーバーおよびデータベースのセキュリティ関連ポリシーを管理できます |
| [SQL Server Contributor](#sql-server-contributor) | SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [Storage Account Contributor](#storage-account-contributor) | ストレージ アカウントを管理できますが、ストレージ アカウントにアクセスすることはできません。 |
| [ストレージ アカウント キー オペレーターのサービス ロール](#storage-account-key-operator-service-role) | ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [Support Request Contributor](#support-request-contributor) | サブスクリプションのスコープでのサポート チケットの作成と管理 |
| [Traffic Manager Contributor](#traffic-manager-contributor) | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
| [User Access Administrator](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理できます |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | このロールが割り当てられたユーザーは、Windows 管理者または Linux の root ユーザー特権で仮想マシンにログインできます。 |
| [Virtual Machine Contributor](#virtual-machine-contributor) | 接続している仮想ネットワークやストレージ アカウント以外の仮想マシンを管理できます |
| [Virtual Machine User Login](#virtual-machine-user-login) | このロールが割り当てられたユーザーは、通常のユーザーとして仮想マシンにログインできます。 |
| [Web Plan Contributor](#web-plan-contributor) | Web プランを管理できます |
| [Website Contributor](#website-contributor) | Web サイトを管理できますが、接続されている Web プランは管理できません |

以降の表は、各ロールに割り当てられている具体的なアクセス許可の説明です。 アクセス許可を与える **Actions** のほか、それらを制限する **NotActions** が含まれている場合があります。

## <a name="owner"></a>Owner
アクセス権を含めすべてを管理できます

| **アクション** |  |
| --- | --- |
| * | あらゆる種類のリソースの作成と管理 |

## <a name="contributor"></a>Contributor
アクセス権以外のすべてを管理できます。

| **アクション** |  |
| --- | --- |
| * | あらゆる種類のリソースの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | ロールとロール割り当ては削除できません |
| Microsoft.Authorization/*/Write | ロールとロール割り当ては作成できません |
| Microsoft.Authorization/elevateAccess/Action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |

## <a name="reader"></a>Reader
すべてを閲覧できますが、変更を加えることはできません

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |

## <a name="api-management-service-contributor"></a>API Management Service Contributor
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | API Management サービスの作成と管理 |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="api-management-service-operator-role"></a>API Management Service Operator Role
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーへの API Management サービスのバックアップ |
| Microsoft.ApiManagement/service/delete | API Management サービス インスタンスの削除 |
| Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数の変更、API Management サービスのリージョン デプロイの追加または削除 |
| Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
| Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの SSL 証明書をアップロードします。 |
| Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名の設定、更新、または削除 |
| Microsoft.ApiManagement/service/write | API Management サービスの新しいインスタンスの作成 |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |

## <a name="api-management-service-reader-role"></a>API Management Service Reader Role
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |
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
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
ジョブを開始、停止、中断、および再開できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/jobs/read | オートメーション アカウント ジョブの読み取り |
| Microsoft.Automation/automationAccounts/jobs/resume/action | オートメーション アカウント ジョブの再開 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | オートメーション アカウント ジョブの停止 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | オートメーション アカウント ジョブ ストリームの読み取り |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | オートメーション アカウント ジョブの中断 |
| Microsoft.Automation/automationAccounts/jobs/write | オートメーション アカウント ジョブの書き込み |
| Microsoft.Automation/automationAccounts/jobSchedules/read | オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/jobSchedules/write | オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
| Microsoft.Automation/automationAccounts/read | オートメーション アカウントの読み取り |
| Microsoft.Automation/automationAccounts/runbooks/read | Automation Runbook の読み取り |
| Microsoft.Automation/automationAccounts/schedules/read | オートメーション アカウント スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/schedules/write | オートメーション アカウント スケジュールの書き込み |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | バックアップの管理に関連するメタ データの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="backup-operator"></a>Backup Operator
コンテナーの作成、バックアップの削除、および他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | バックアップ管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 保護コンテナーに対する操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | バックアップされたアイテムに対するオンデマンドのバックアップ操作の実行 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | バックアップされたアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | バックアップされたアイテムの復旧ポイントの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | バックアップされた項目の復旧ポイントを使用した復元操作の実行 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | バックアップ アイテムの作成 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | バックアップ アイテムを保持するコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | ジョブを取り消します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | バックアップの管理に関連するメタ データの読み取り |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | バックアップ ポリシーに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | バックアップ ポリシーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | すべての保護可能な項目の一覧を返します。 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | バックアップ アイテムを保持するバックアップされたコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | コンテナーに関連する拡張情報の書き込み |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | コンテナーの登録済みアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | コンテナーの登録済みアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | コンテナーの登録済みアイテムの書き込み |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
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
Recovery Services コンテナーでのバックアップ管理を監視できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | バックアップ管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 保護コンテナーに対する操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | バックアップされたアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | バックアップ アイテムを保持するコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | バックアップ ジョブの結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | バックアップ ジョブの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | バックアップの管理に関連するメタ データの読み取り |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | バックアップの管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | バックアップ ポリシーに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | バックアップ ポリシーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | バックアップ アイテムを保持するバックアップされたコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | コンテナーの登録済みアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | コンテナーの登録済みアイテムの読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の読み取り |

## <a name="billing-reader"></a>Billing Reader
すべての課金情報を見ることができます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Billing/*/read | 課金情報の読み取り |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="biztalk-contributor"></a>BizTalk Contributor
BizTalk Services を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
従来の仮想ネットワークと予約済み IP を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicNetwork/* | 従来のネットワークの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-contributor"></a>Classic Storage Account Contributor
従来のストレージ アカウントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicStorage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-key-operator-service-role"></a>従来のストレージ アカウント キー オペレーターのサービス ロール
従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます

| **アクション** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |

## <a name="classic-virtual-machine-contributor"></a>Classic Virtual Machine Contributor
接続している仮想ネットワークやストレージ アカウント以外の従来の仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicCompute/domainNames/* | 従来のコンピューティング ドメイン名の作成と管理 |
| Microsoft.ClassicCompute/virtualMachines/* | 仮想マシンの作成と管理 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | ネットワーク セキュリティ グループの接続 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP へのリンク付け |
| Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP アドレスの読み取り |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークの接続 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクの読み取り |
| Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージの読み取り |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウント キーの一覧表示 |
| Microsoft.ClassicStorage/storageAccounts/read | 従来のストレージ アカウントの読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB Contributor
ClearDB MySQL データベースを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| successbricks.cleardb/databases/* | ClearDB MySQL データベースの作成と管理 |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB アカウントの閲覧者ロール
Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り (各ユーザーに付与されたアクセス許可を読み取ることができます) |
| Microsoft.DocumentDB/*/read | 任意のコレクションの読み取り |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 読み取り専用キー ウィンドウの読み取り |
| Microsoft.Insights/MetricDefinitions/read | メトリック定義の読み取り |
| Microsoft.Insights/Metrics/read | アカウント メトリックの読み取り |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="data-factory-contributor"></a>Data Factory Contributor
Data Factory と Data Factory に含まれる子リソースを作成および管理します。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DataFactory/dataFactories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
| Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンの割り当て解除 |
| Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/restart/action | 仮想マシンの再起動 |
| Microsoft.Compute/virtualMachines/start/action | 仮想マシンの開始 |
| Microsoft.DevTestLab/*/read | ラボのプロパティの読み取り |
| Microsoft.DevTestLab/labs/createEnvironment/action | ラボ環境の作成 |
| Microsoft.DevTestLab/labs/claimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
| Microsoft.DevTestLab/labs/formulas/delete | 数式の削除 |
| Microsoft.DevTestLab/labs/formulas/read | 数式の読み取り |
| Microsoft.DevTestLab/labs/formulas/write | 数式の追加または変更 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | ラボのポリシーの評価 |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | 既存の仮想マシンの所有権を取得します。 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則の接続 |
| Microsoft.Network/networkInterfaces/*/read | ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
| Microsoft.Network/networkInterfaces/join/action | ネットワーク インターフェイスへの仮想マシンの接続 |
| Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの読み取り |
| Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスの作成 |
| Microsoft.Network/publicIPAddresses/*/read | パブリック IP アドレスのプロパティの読み取り |
| Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスの接続 |
| Microsoft.Network/publicIPAddresses/read | ネットワーク パブリック IP アドレスの読み取り |
| Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークの接続 |
| Microsoft.Resources/deployments/operations/read | デプロイ操作の読み取り |
| Microsoft.Resources/deployments/read | デプロイの読み取り |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/listKeys/action | ストレージ アカウント キーの一覧表示 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |

## <a name="dns-zone-contributor"></a>DNS Zone Contributor
DNS ゾーンとレコードを保護できます。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/dnsZones/* | DNS ゾーンとレコードの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor
Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems Account Contributor
Intelligent Systems アカウントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
| Microsoft.OperationalInsights/workspaces/search/action | Log Analytics ワークスペースを検索します。 |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Log Analytics ワークスペースのキーを一覧表示します。 |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Monitoring Reader
すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。

| **アクション** |  |
| --- | --- |
| */read | 機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.OperationalInsights/workspaces/search/action | Log Analytics データの検索 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="network-contributor"></a>ネットワークの共同作業者
すべてのネットワーク リソースを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/* | ネットワークの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
Redis キャッシュを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cache/redis/* | Redis キャッシュの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collections Contributor
Scheduler Job Collection を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Scheduler/jobcollections/* | ジョブ コレクションの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="search-service-contributor"></a>Search Service Contributor
Search サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |
| Microsoft.Security/locations/alerts/dismiss/action | セキュリティ アラートを無視します |
| Microsoft.Security/locations/tasks/dismiss/action | セキュリティ推奨事項を無視します。 |
| Microsoft.Security/policies/write | セキュリティ ポリシーを更新します。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-manager"></a>Security Manager
セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.ClassicCompute/*/read | 従来の仮想マシンの構成情報の読み取り |
| Microsoft.ClassicCompute/virtualMachines/*/write | 従来の仮想マシンの構成の書き込み |
| Microsoft.ClassicNetwork/*/read | 従来のネットワークに関する構成情報の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |

## <a name="site-recovery-contributor"></a>Site Recovery Contributor
Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべての Site Recovery 管理アクションを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/certificates/write | コンテナー資格情報証明書の更新 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-operator"></a>Site Recovery Operator
フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作の実行や、他のユーザーへのアクセス権の割り当てはできません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 操作の状態と送信された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | リソースに対する登録済みコンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | レプリケーションの警告の設定の読み取り |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性の確認 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | レプリケーション ファブリックの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | レプリケーション ゲートウェイの再関連付け |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | レプリケーション ファブリックの証明書の書き換え |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | レプリケーション ファブリック ネットワークの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | レプリケーション ファブリック ネットワーク マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 保護コンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | すべての保護可能な項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | すべての保護された項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 使用可能な回復ポイントの一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目の再保護の開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | 保護された項目のテスト フェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | フェールオーバー |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services プロバイダーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Recovery Services プロバイダーの更新 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | レプリケーション ファブリック用のストレージの分類の読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージの分類マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 登録済みの vCenter 情報の読み取り |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | レプリケーション ポリシーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 復旧計画のフェールオーバー用のフェールオーバーのコミット |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 復旧計画のフェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画の読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 復旧計画の再保護の開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 復旧計画のテスト フェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 復旧計画のテスト フェールオーバーのクリーンアップの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 復旧計画の計画されていないフェールオーバーの開始 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Recovery Services コンテナーのストレージ構成の読み取り |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-reader"></a>Site Recovery Reader
Recovery Services コンテナーで Site Recovery の状態を監視し、サポート チケットを発生させることができます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 操作の状態と送信された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | リソースに対する登録済みコンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | レプリケーションの警告の設定の読み取り |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | レプリケーション ファブリックの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | レプリケーション ファブリック ネットワークの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | レプリケーション ファブリック ネットワーク マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 保護コンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | すべての保護可能な項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | すべての保護された項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 使用可能な回復ポイントの一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services プロバイダーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | レプリケーション ファブリック用のストレージの分類の読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージの分類マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 登録済みの vCenter 情報の読み取り |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | レプリケーション ジョブの状態の読み取り |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | レプリケーション ポリシーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画の読み取り |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Recovery Services コンテナーのストレージ構成の読み取り |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-db-contributor"></a>SQL DB Contributor
SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | SQL データベースの作成と管理 |
| Microsoft.Sql/servers/read | SQL Server の読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 監査ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/auditingSettings/* | 監査設定を編集することはできません |
| Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードを読み取ることはできません |
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
SQL サーバーおよびデータベースのセキュリティ関連ポリシーを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Microsoft 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードの読み取り |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/read | SQL データベースの読み取り |
| Microsoft.Sql/servers/databases/schemas/read | SQL サーバー データベース スキーマの読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | SQL サーバー データベース テーブル列の読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | SQL サーバー データベース テーブルの読み取り |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | SQL Server の読み取り |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-server-contributor"></a>SQL Server Contributor
SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
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
ストレージ アカウントを管理できますが、ストレージ アカウントにアクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | あらゆる承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* | 診断設定の管理 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="storage-account-key-operator-service-role"></a>ストレージ アカウント キー オペレーターのサービス ロール
ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます

| **アクション** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
| Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |

## <a name="support-request-contributor"></a>Support Request Contributor
サブスクリプションのスコープでのサポート チケットの作成と管理

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |
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

## <a name="user-access-administrator"></a>ユーザーアクセスの管理者
Azure リソースに対するユーザー アクセスを管理できます

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
接続している仮想ネットワークやストレージ アカウント以外の仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Compute/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
| Microsoft.Compute/locations/* | コンピューティングの場所の作成と管理 |
| Microsoft.Compute/virtualMachines/* | 仮想マシンの作成と管理 |
| Microsoft.Compute/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | ネットワーク アプリケーション ゲートウェイのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則の接続 |
| Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 |
| Microsoft.Network/loadBalancers/read | ロード バランサーの読み取り |
| Microsoft.Network/locations/* | ネットワークの場所の作成と管理 |
| Microsoft.Network/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
| Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループの接続 |
| Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの読み取り |
| Microsoft.Network/publicIPAddresses/join/action | ネットワーク パブリック IP アドレスの接続 |
| Microsoft.Network/publicIPAddresses/read | ネットワーク パブリック IP アドレスの読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワーク サブネットの接続 |
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
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/listKeys/action | ストレージ アカウント キーの一覧表示 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="virtual-machine-user-login"></a>Virtual Machine User Login
このロールが割り当てられたユーザーは、通常のユーザーとして仮想マシンにログインできます。

| **アクション** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Web Plan Contributor
Web プランを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/serverFarms/* | サーバー ファームの作成と管理 |

## <a name="website-contributor"></a>Website Contributor
Web サイトを管理できますが、接続されている Web プランは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/certificates/* | Web サイト証明書の作成と管理 |
| Microsoft.Web/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの読み取り |
| Microsoft.Web/serverFarms/join/action | サーバー ファームの接続 |
| Microsoft.Web/serverFarms/read | サーバー ファームの読み取り |
| Microsoft.Web/sites/* | Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |

## <a name="see-also"></a>関連項目
* [ロールベースのアクセス制御](role-based-access-control-configure.md): Azure Portal での RBAC の基本について説明します。
* [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md): アクセスのニーズに合わせてカスタム ロールを作成する方法について説明します。
* [アクセス変更履歴レポートの作成](role-based-access-control-access-change-history-report.md): RBAC でのロール割り当ての変更を追跡します。
* [ロールベースのアクセス制御のトラブルシューティング](role-based-access-control-troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
* [Azure Security Center におけるアクセス許可](../security-center/security-center-permissions.md)
