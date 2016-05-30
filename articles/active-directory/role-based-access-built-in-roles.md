<properties
	pageTitle="RBAC: 組み込みのロール |Microsoft Azure"
	description="このトピックでは、ロール ベースのアクセス制御 (RBAC) の組み込みのロールについて説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/16/2016"
	ms.author="kgremban"/>

#RBAC: 組み込みのロール

## 組み込みのロール

Azure のロールベースのアクセス制御 (RBAC) には、ユーザー、グループ、サービスに割り当てられる次の組み込みのロールが用意されています。組み込みのロールの定義は変更できません。ただし、組織の具体的なニーズに合うように [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md)を作成することができます。

次の表には、組み込みのロールについての簡単な説明を示します。ロール名をクリックすると、そのロールの **actions** と **not actions** の詳細な一覧を確認できます。**actions** プロパティは、Azure リソースに対して許可するアクションを指定します。アクションの文字列にワイルドカード文字を使用できます。**not actions** プロパティは、許可するアクションから除外されるアクションを指定します。

>[AZURE.NOTE] Azure のロール定義は常に進化しています。この記事は、最新の状態であることを心掛けておりますが、Azure PowerShell で常に最新のロール定義を見つけることができます。適宜、コマンドレット `(get-azurermroledefinition "<role name>").actions` または `(get-azurermroledefinition "<role name>").notactions` を使用します。

| ロール名 | 説明 |
| --------- | ----------- |
| [API 管理サービスの共同作業者](#api-management-service-contributor) | API Management サービスを管理できます |
| [Application Insights コンポーネントの共同作業者](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます |
| [オートメーション オペレーター](#automation-operator) | ジョブを開始、停止、中断、および再開できます |
| [BizTalk の共同作業者](#biztalk-contributor) | BizTalk Services を管理できます |
| [ClearDB MySQL DB の共同作業者](#cleardb-mysql-db-contributor) | ClearDB MySQL データベースを管理できます |
| [共同作成者](#contributor) | アクセス権以外のすべてを管理できます。 |
| [Data Factory の共同作業者](#data-factory-contributor) | Data Factory を管理できます |
| [DevTest Labs ユーザー](#devtest-labs-user) | すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます |
| [Document DB アカウントの共同作業者](#documentdb-account-contributor) | DocumentDB アカウントを管理できます |
| [Intelligent Systems アカウントの共同作業者](#intelligent-systems-account-contributor) | Intelligent Systems アカウントを管理できます |
| [ネットワークの共同作業者](#network-contributor) | すべてのネットワーク リソースを管理できます |
| [New Relic APM アカウントの共同作業者](#new-relic-apm-account-contributor) | New Relic Application Performance Management アカウントおよびアプリケーションを管理できます |
| [所有者](#owner) | アクセス権を含めすべてを管理できます |
| [閲覧者](#reader) | すべてを閲覧できますが、変更を加えることはできません |
| [Redis Cache の共同作業者](#redis-cache-contributor]) | Redis キャッシュを管理できます |
| [Scheduler Job Collection の共同作業者](#scheduler-job-collections-contributor) | Scheduler Job Collection を管理できます |
| [Search サービスの共同作業者](#search-service-contributor) | Search サービスを管理できます |
| [セキュリティ管理者](#security-manager) | セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます |
| [SQL DB の共同作業者](#sql-db-contributor) | SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [SQL セキュリティ管理者](#sql-security-manager) | SQL Server やデータベースのセキュリティ関連ポリシーを管理できます |
| [SQL Server の共同作業者](#sql-server-contributor) | SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [従来のストレージ アカウントの共同作業者](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できます |
| [ストレージ アカウントの共同作業者](#storage-account-contributor) | ストレージ アカウントを管理できます |
| [ユーザーアクセスの管理者](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理できます |
| [従来の仮想マシンの共同作業者](#classic-virtual-machine-contributor) | クラシック仮想マシンを管理できますが、その接続先の仮想ネットワークやストレージ アカウントは管理できません |
| [仮想マシンの共同作業者](#virtual-machine-contributor) | 仮想マシンを管理できますが、その接続先の仮想ネットワークやストレージ アカウントは管理できません |
| [従来のネットワークの共同作業者](#classic-network-contributor) | 従来の仮想ネットワークと予約済み IP を管理できます |
| [Web プランの共同作業者](#web-plan-contributor) | Web プランを管理できます |
| [Web サイトの共同作業者](#website-contributor) | Web サイトを管理できますが、その接続先の Web プランは管理できません |

### API Management サービスの共同作業者
API Management サービスを管理できます

| **アクション** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | API Management サービスの作成と管理 |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### Application Insights コンポーネントの共同作業者
Application Insights コンポーネントを管理できます

| **アクション** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.Insights/webtests/* | Web テストの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### オートメーション オペレーター
ジョブを開始、停止、中断、および再開できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/jobs/read | オートメーション アカウント ジョブの読み取り |
| Microsoft.Automation/automationAccounts/jobs/resume/action | オートメーション アカウント ジョブの再開 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | オートメーション アカウント ジョブの停止 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | オートメーション アカウント ジョブ ストリームの読み取り |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | オートメーション アカウント ジョブの中断 |
| Microsoft.Automation/automationAccounts/jobs/write | オートメーション アカウント ジョブの書き込み |
| Microsoft.Automation/automationAccounts/jobSchedules/read | オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/jobSchedules/write | オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/read | オートメーション アカウントの読み取り |
| Microsoft.Automation/automationAccounts/runbooks/read | Automation Runbook の読み取り |
| Microsoft.Automation/automationAccounts/schedules/read | オートメーション アカウント スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/schedules/write | オートメーション アカウント スケジュールの書き込み |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### BizTalk の共同作業者
BizTalk Services を管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### ClearDB MySQL DB の共同作業者
ClearDB MySQL データベースを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| successbricks.cleardb/databases/* | ClearDB MySQL データベースの作成と管理 |

### 共同作成者
アクセス権以外のすべてを管理できます。

| **アクション** ||
| ------- | ------ |
| * | あらゆる種類のリソースの作成と管理 |

| **not actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | ロールとロール割り当ては作成できません |
| Microsoft.Authorization/*/Delete | ロールとロール割り当ては削除できません |

### Data Factory の共同作業者
Data Factory を管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DataFactory/dataFactories/* | Data Factory の作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### DevTest Labs ユーザー
すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
| Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンの割り当て解除 |
| Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/restart/action | 仮想マシンの再起動 |
| Microsoft.Compute/virtualMachines/start/action | 仮想マシンの開始 |
| Microsoft.DevTestLab/*/read | ラボのプロパティの読み取り |
| Microsoft.DevTestLab/labs/createEnvironment/action | ラボ環境の作成 |
| Microsoft.DevTestLab/labs/formulas/delete | 数式の削除 |
| Microsoft.DevTestLab/labs/formulas/read | 数式の読み取り |
| Microsoft.DevTestLab/labs/formulas/write | 数式の追加または変更 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | ラボのポリシーの評価 |
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

### Document DB アカウントの共同作業者
DocumentDB アカウントを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.DocumentDb/databaseAccounts/* | DocumentDB アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### Intelligent Systems アカウントの共同作業者
Intelligent Systems アカウントを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### ネットワークの共同作業者
すべてのネットワーク リソースを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/* | ネットワークの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### New Relic APM アカウントの共同作業者
New Relic Application Performance Management アカウントおよびアプリケーションを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| NewRelic.APM/accounts/* | New Relic Application Performance Management アカウントの作成と管理 |

### 所有者
アクセス権を含めすべてを管理できます

| **アクション** ||
| ------- | ------ |
| * | あらゆる種類のリソースの作成と管理 |

### 閲覧者
すべてを閲覧できますが、変更を加えることはできません

| **アクション** ||
| ------- | ------ |
| **/read | 機密データを除くあらゆる種類のリソースの読み取り |

### Redis Cache の共同作業者
Redis キャッシュを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Cache/redis/* | Redis キャッシュの作成と管理 |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### Scheduler Job Collection の共同作業者
Scheduler ジョブ コレクションを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Scheduler/jobcollections/* | ジョブ コレクションの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### Search サービスの共同作業者
Search サービスを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Search/searchServices/* | 検索サービスの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### セキュリティ管理者
セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.ClassicCompute/*/read | 従来のコンピューティング仮想マシンの構成情報の読み取り |
| Microsoft.ClassicCompute/virtualMachines/*/write | 仮想マシンの構成の書き込み |
| Microsoft.ClassicNetwork/*/read | 従来のネットワークに関する構成情報の読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Security/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### SQL DB の共同作業者
SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Sql/servers/databases/* | SQL データベースの作成と管理 |
| Microsoft.Sql/servers/read | SQL Server の読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **not actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 監査ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/auditingSettings/* | 監査設定を編集することはできません |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 接続ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | データ マスク ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | セキュリティの警告のポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/securityMetrics/* | セキュリティ基準を編集することはできません |

### SQL セキュリティ管理者
SQL Server やデータベースのセキュリティ関連ポリシーを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Microsoft 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/read | SQL データベースの読み取り |
| Microsoft.Sql/servers/databases/schemas/read | SQL サーバー データベース スキーマの読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | SQL サーバー データベース テーブル列の読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/read | SQL サーバー データベース テーブルの読み取り |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
| Microsoft.Sql/servers/read | SQL Server の読み取り |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### SQL Server の共同作業者
SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り|
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Sql/servers/* | SQL サーバーの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

| **not actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーは編集できません |
| Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定は編集できません |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーは編集できません |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定は編集できません |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーは編集できません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーは編集できません |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーは編集できません |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準は編集できません |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーは編集できません |

### 従来のストレージ アカウントの共同作業者
従来のストレージ アカウントを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicStorage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Support/* | サポート チケットの作成と管理 |

### ストレージ アカウントの共同作業者
アクセス権以外のストレージ アカウントを管理できます。

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | あらゆる承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* | 診断設定の管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### ユーザーアクセスの管理者
Azure リソースに対するユーザー アクセスを管理できます

| **アクション** ||
| ------- | ------ |
| */read | 機密データを除くあらゆる種類のリソースの読み取り | | Microsoft.Authorization/* | 承認の読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### 従来の仮想マシンの共同作業者
接続している仮想ネットワークやストレージ アカウント以外の従来の仮想マシンを管理できます

| **アクション** ||
| ------- | ------ |
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
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Support/* | サポート チケットの作成と管理 |

### 仮想マシンの共同作業者
接続している仮想ネットワークやストレージ アカウント以外の仮想マシンを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Compute/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
| Microsoft.Compute/locations/* | コンピューティングの場所の作成と管理 |
| Microsoft.Compute/virtualMachines/* | 仮想マシンの作成と管理 |
| Microsoft.Compute/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | ネットワーク アプリケーション ゲートウェイのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則の接続 |
| Microsoft.Network/loadBalancers/read | ロード バランサーの読み取り |
| Microsoft.Network/locations/* | ネットワークの場所の作成と管理 |
| Microsoft.Network/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
| Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループの接続 |
| Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの読み取り |
| Microsoft.Network/publicIPAddresses/join/action | ネットワーク パブリック IP アドレスの接続 |
| Microsoft.Network/publicIPAddresses/read | ネットワーク パブリック IP アドレスの読み取り |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワーク サブネットの接続 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Storage/storageAccounts/listKeys/action | ストレージ アカウント キーの一覧表示 |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### 従来のネットワークの共同作業者
従来の仮想ネットワークと予約済み IP を管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.ClassicNetwork/* | 従来のネットワークの作成と管理 |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Support/* | サポート チケットの作成と管理 |

### Web Plan の共同作業者
Web プランを管理できます

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/serverFarms/* | サーバー ファームの作成と管理 |

### Web サイトの共同作業者
Web サイトを管理できますが、接続されている Web プランは管理できません

| **アクション** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
| Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り | Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Web/certificates/* | Web サイト証明書の作成と管理 |
| Microsoft.Web/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの読み取り |
| Microsoft.Web/serverFarms/join/action | サーバー ファームの接続 |
| Microsoft.Web/serverFarms/read | サーバー ファームの読み取り |
| Microsoft.Web/sites/* | Web サイトの作成と管理 |

## 関連項目
- [Azure Active Directory リソースへのアクセスをロールの割り当てによって管理する](role-based-access-control-configure.md): Azure ポータルでの RBAC の基本。
- [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md): アクセスのニーズに合わせてカスタム ロールを作成する方法について説明します。
- [アクセス変更履歴レポートの作成](role-based-access-control-access-change-history-report.md): RBAC でのロール割り当ての変更を追跡します。
- [ロールベースのアクセス制御のトラブルシューティング](role-based-access-control-troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。

<!---HONumber=AcomDC_0518_2016-->