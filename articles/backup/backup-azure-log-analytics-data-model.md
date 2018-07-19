---
title: Azure Backup の Log Analytics データ モデル
description: この記事では、Azure Backup データに使用する Log Analytics データ モデルの詳細について説明します。
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 32ce9cb456e302647e2e09db6ed549cd4795fed7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448392"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup データの Log Analytics データ モデル
レポートの作成には、Log Analytics データ モデルを使用します。 データ モデルでは、カスタム クエリとダッシュボードを作成したり、Azure Backup データを自由にカスタマイズしたりすることができます。

## <a name="using-azure-backup-data-model"></a>Azure Backup データ モデルを使用する
データ モデルの一部として提供される次のフィールドを使用して、ビジュアル、カスタム クエリ、ダッシュボードを要件に合わせて作成できます。

### <a name="alert"></a>アラート:
次の表は、アラートに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| AlertUniqueId_s |テキスト |生成されたアラートの一意の識別子 |
| AlertType_s |テキスト |アラートの種類 (例: バックアップ) |
| AlertStatus_s |テキスト |アラートの状態 (例: アクティブ) |
| AlertOccurenceDateTime_s |日付/時刻 |アラートが作成された日付と時刻 |
| AlertSeverity_s |テキスト |アラートの重大度 (例: 重大) |
| EventName_s |テキスト |イベントの名前。 常に AzureBackupCentralReport |
| BackupItemUniqueId_s |テキスト |アラートに関連付けられているバックアップ項目の一意の識別子 |
| SchemaVersion_s |テキスト |スキーマの現在のバージョン (例: **V1**) |
| State_s |テキスト |アラート オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このアラートが属する FileFolder) |
| OperationName |テキスト |現在の操作の名前 (例: アラート) |
| カテゴリ |テキスト |Log Analytics にプッシュされた診断データのカテゴリ。 常に AzureBackupReport |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |アラートに関連付けられている保護されるサーバーの一意の識別子 |
| VaultUniqueId_s |テキスト |アラートに関連付けられている保護されるコンテナーの一意の識別子 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |データの収集対象のリソースの一意の識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="backupitem"></a>BackupItem
次の表は、バックアップ項目に関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |イベントの名前。 常に AzureBackupCentralReport |  
| BackupItemUniqueId_s |テキスト |バックアップ項目の一意の識別子 |
| BackupItemId_s |テキスト |バックアップ項目の識別子 |
| BackupItemName_s |テキスト |バックアップ項目の名前 |
| BackupItemFriendlyName_s |テキスト |バックアップ項目のフレンドリ名 |
| BackupItemType_s |テキスト |バックアップ項目の種類 (例: VM、FileFolder) |
| ProtectedServerName_s |テキスト |バックアップ項目が属している保護されるサーバーの名前 |
| ProtectionState_s |テキスト |バックアップ項目の現在の保護状態 (例: 保護済み、保護停止) |
| SchemaVersion_s |テキスト |スキーマのバージョン (例: **V1**) |
| State_s |テキスト |バックアップ項目オブジェクトの状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このバックアップ項目が属する FileFolder) |
| OperationName |テキスト |操作の名前 (例: BackupItem) |
| カテゴリ |テキスト |Log Analytics にプッシュされた診断データのカテゴリ。 常に AzureBackupReport |
| リソース |テキスト |データの収集対象のリソース (例: Recovery Services コンテナー名) |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース ID (例: Recovery Services コンテナーのリソース ID) |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |収集されるデータのリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |収集されるデータのリソース プロバイダー (例: Microsoft.RecoveryServices) |
| ResourceType |テキスト |収集されるデータのリソースの種類 (例: コンテナー) |

### <a name="backupitemassociation"></a>BackupItemAssociation
次の表は、バックアップ項目 アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |  
| BackupItemUniqueId_s |テキスト |バックアップ項目の一意の ID |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |バックアップ項目アソシエーション オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (BackupItemAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |バックアップ項目に関連付けられているポリシーの一意の識別子 |
| ProtectedServerUniqueId_s |テキスト |バックアップ項目に関連付けられている保護されるサーバーの一意の識別子 |
| VaultUniqueId_s |テキスト |バックアップ項目を含むコンテナーの一意の識別子 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |収集されるデータのリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |収集されるデータのリソース プロバイダー (例: Microsoft.RecoveryServices) |
| ResourceType |テキスト |収集されるデータのリソースの種類 (例: コンテナー) |

### <a name="job"></a>ジョブ
次の表は、ジョブに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |イベントの名前。 常に AzureBackupCentralReport |
| BackupItemUniqueId_s |テキスト |バックアップ項目の一意の識別子 |
| SchemaVersion_s |テキスト |スキーマのバージョン (例: **V1**) |
| State_s |テキスト |ジョブ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Job) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |ジョブに関連付けられている保護されるサーバーの一意の識別子 |
| VaultUniqueId_s |テキスト |保護されるコンテナーの一意の識別子 |
| JobOperation_s |テキスト |ジョブを実行する対象の操作 (例: バックアップ、復元、バックアップの構成) |
| JobStatus_s |テキスト |完了したジョブの状態 (例: 完了、失敗) |
| JobFailureCode_s |テキスト |ジョブ エラーが発生したことによるエラー コードの文字列 |
| JobStartDateTime_s |日付/時刻 |ジョブの実行を開始した日付と時刻 |
| BackupStorageDestination_s |テキスト |バックアップ ストレージの保存先 (例: クラウド、ディスク)  |
| JobDurationInSecs_s | Number |合計ジョブ期間 (秒単位) |
| DataTransferredInMB_s | Number |このジョブで転送されたデータ (MB)|
| JobUniqueId_g |テキスト |ジョブを識別する一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID|
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="policy"></a>ポリシー
次の表は、ポリシーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Policy) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |ポリシーを識別する一意の ID |
| PolicyName_s |テキスト |定義されたポリシーの名前 |
| BackupFrequency_s |テキスト |バックアップが実行される頻度 (例: 毎日、毎週) |
| BackupTimes_s |テキスト |バックアップがスケジュールされている日付と時刻 |
| BackupDaysOfTheWeek_s |テキスト |バックアップがスケジュールされている曜日 |
| RetentionDuration_s |整数 |構成されたバックアップに使用されるリテンション期間 |
| DailyRetentionDuration_s |整数 |構成されたバックアップに使用される合計リテンション期間 (日数) |
| DailyRetentionTimes_s |テキスト |毎日のリテンション期間が構成された日付と時刻 |
| WeeklyRetentionDuration_s |10 進数 |構成されたバックアップの毎週の合計リテンション期間 (週単位) |
| WeeklyRetentionTimes_s |テキスト |毎週のリテンション期間が構成される日付と時刻 |
| WeeklyRetentionDaysOfTheWeek_s |テキスト |毎週のリテンション期間に選択された曜日 |
| MonthlyRetentionDuration_s |10 進数 |構成されたバックアップに使用される合計のリテンション期間 (月単位) |
| MonthlyRetentionTimes_s |テキスト |毎月のリテンション期間が構成される日付と時刻 |
| MonthlyRetentionFormat_s |テキスト |毎月のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| MonthlyRetentionDaysOfTheWeek_s |テキスト |毎月のリテンション期間に選択された曜日 |
| MonthlyRetentionWeeksOfTheMonth_s |テキスト |毎月のリテンション期間が構成されたときの月の週 (例: 最初、最後など) |
| YearlyRetentionDuration_s |10 進数 |構成されたバックアップに使用される合計リテンション期間 (年単位) |
| YearlyRetentionTimes_s |テキスト |毎年のリテンション期間が構成される日付と時刻 |
| YearlyRetentionMonthsOfTheYear_s |テキスト |毎年のリテンション期間に選択された月 |
| YearlyRetentionFormat_s |テキスト |毎年のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| YearlyRetentionDaysOfTheMonth_s |テキスト |毎年のリテンション期間に選択された月の日数 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="policyassociation"></a>PolicyAssociation
次の表は、ポリシー アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (PolicyAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |ポリシーを識別する一意の ID |
| VaultUniqueId_s |テキスト |ポリシーが属しているコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="protectedserver"></a>ProtectedServer
次の表は、保護されるサーバーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| ProtectedServerName_s |テキスト |保護されるサーバーの名前 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |保護されるサーバー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (ProtectedServer) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |保護されるサーバーの一意の ID |
| RegisteredContainerId_s |テキスト |バックアップ用に登録されたコンテナーの ID |
| ProtectedServerType_s |テキスト |保護されるサーバーの種類 (例: Windows) |
| ProtectedServerFriendlyName_s |テキスト |保護されるサーバーのフレンドリ名 |
| AzureBackupAgentVersion_s |テキスト |エージェント バックアップ バージョンのバージョン番号 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
次の表は、保護されるサーバー アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |保護されるサーバー アソシエーション オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (ProtectedServerAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |保護されるサーバーの一意の ID |
| VaultUniqueId_s |テキスト |この保護されるサーバーが属しているコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="storage"></a>Storage
次の表は、ストレージに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| CloudStorageInBytes_s |10 進数 |最新の値に基づいて計算された、バックアップによって使用されるクラウド バックアップ ストレージ |
| ProtectedInstances_s |10 進数 |最新の値に基づいて計算された、課金されるフロントエンド ストレージの計算に使用する保護されるインスタンスの数 |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ストレージ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Storage) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |ストレージを計算するために使用する保護されるサーバーの一意の ID |
| VaultUniqueId_s |テキスト |ストレージを計算するために使用するコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="vault"></a>コンテナー
次の表は、コンテナーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |コンテナー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Vault) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| VaultUniqueId_s |テキスト |コンテナーの一意の ID |
| VaultName_s |テキスト |コンテナーの名前 |
| AzureDataCenter_s |テキスト |コンテナーが配置されるデータ センター |
| StorageReplicationType_s |テキスト |コンテナーのストレージ レプリケーションの種類 (例: GeoRedundant) |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |テキスト |データの収集対象のリソース (例:  Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |テキスト |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |テキスト |データの収集対象のリソースの種類。 たとえば、コンテナー |

## <a name="next-steps"></a>次の手順
Azure Backup のレポートを作成するためのデータ モデルを確認したら、Log Analytics で[ダッシュボードの作成](../log-analytics/log-analytics-dashboards.md)を開始できます。
