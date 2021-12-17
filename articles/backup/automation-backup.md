---
title: Azure Backup での自動化
description: ユーザーが提供する自動化機能の概要を示Azure Backup
ms.topic: conceptual
ms.date: 11/16/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: a836f7e92c5de839ac093816e5935b08a8283e92
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716735"
---
# <a name="automation-in-azure-backup"></a>Azure Backup での自動化

Azure で操作を管理する場合、新しい多様なワークロードセットやスケールアップをボリュームに含めて、バックアップ資産が拡張される可能性があります。

小規模では、バックアップする適切なマシンの特定、バックアップの構成、状態の監視、データの抽出など、次のアクションを手動で実行したり、簡単に実行することができます。 しかし、大規模な場合、このようなアクションは困難で複雑になり、エラーが発生する可能性があります。

Azure Backupプログラムによる方法を使用して、ほとんどのバックアップ タスクを自動化できます。 このドキュメントでは、このドキュメントでサポートされているさまざまなオートメーション クライアントAzure Backup。 また、エンタープライズ規模のバックアップデプロイで一般的に使用されるエンドツーエンドの自動化シナリオについて説明します。

## <a name="automation-methods"></a>オートメーション メソッド

この機能Azure Backupアクセスするには、Azure でサポートされている次の標準的な自動化方法を使用できます。

- PowerShell
- CLI
- REST API
- Python SDK
- Go SDK
- Terraform
- ARM テンプレート
- Bicep

Logic Apps、Runbook、アクション グループなど、他の Azure サービスに関連付けられている Azure Backup を使用して、エンドツーエンドの自動化ワークフローを設定することもできます。

オートメーション クライアントがサポートするさまざまなシナリオと、対応するドキュメントリファレンスの詳細については、「サポートされるオートメーション ソリューション」セクションを[Azure Backupしてください。](#supported-automation-methods-by-operation-types)

## <a name="sample-automation-scenarios"></a>サンプルの自動化シナリオ

このセクションでは、バックアップ管理者として発生する可能性があるいくつかの一般的な自動化の使用例を示します。また、開始に関するガイダンスも提供します。

### <a name="configure-backups"></a>バックアップを構成する

バックアップ管理者は、定期的に追加される新しいインフラストラクチャに対処し、合意された要件に従って保護されている必要があります。 PowerShell/CLI などのオートメーション クライアントは、すべての VM の詳細をフェッチし、各 VM のバックアップ状態を確認し、保護されていない VM に対して適切なアクションを実行するのに役立ちます。

ただし、これは大規模にパフォーマンスを高くする必要があります。 また、定期的にスケジュールを設定し、各実行を監視する必要があります。 自動化操作を容易にするために、Azure Backup は Azure Policy を使用し、バックアップ資産を管理する組み込みのバックアップ固有 [の Azure](backup-center-govern-environment.md#azure-policies-for-backup) ポリシーを提供します。

Azure ポリシーをスコープに割り当てると、条件を満たすすべての VM が自動的にバックアップされ、新しい VM がスキャンされ、Azure Policy によって定期的に保護されます。 準拠していないリソースについて警告するコンプライアンス レポートを表示することもできます。

[バックアップ用の組み込みの Azure ポリシーの詳細を確認します](backup-azure-auto-enable-backup.md)。

次のビデオでは、バックアップのAzure Policyする方法を示します。  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>バックアップ操作データをエクスポートする

資産全体のバックアップ運用データを抽出し、監視システム/ダッシュボードに定期的にインポートする必要がある場合があります。 大規模な場合は、データを高速に取得する必要があります (大量のレコードに対してクエリを実行する間)。 リソース、サブスクリプション、テナント間でクエリを実行する必要があります。 また、クライアント (Azure portal/PowerShell/CLI/any SDK/REST API) からクエリを実行する必要があります。 出力形式 (テーブルと配列) にも柔軟性が必要です。

Azure Resource Graph (ARG) を使用すると、これらの操作を実行し、大規模にクエリを実行できます。 Azure Backupクエリ (1 つのシナリオに対して 1 つのクエリ) で必要なデータをフェッチするための最適化された方法として ARG を使用します。 たとえば、1 つのクエリで、すべてのサブスクリプションとすべてのテナントのすべてのコンテナーで、失敗したジョブをフェッチできます。 また、クエリは Azure ロールベースのアクセス制御 (Azure RBAC) に準拠しています。

ARG クエリ [のサンプルを参照してください](query-backups-using-azure-resource-graph.md#sample-queries)。

### <a name="automate-responsesactions"></a>応答/アクションを自動化する

一時的なバックアップ ジョブの失敗に対する応答を自動化することで、復元元の信頼性の高いバックアップの数を適切に確保できます。 これは、RPO での意図しない侵害を回避するのに [も役立ちます](azure-backup-glossary.md#rpo-recovery-point-objective)。

Runbook、PowerShell、Azure Resource Azure Automation の組み合わせを使用して、バックアップ ジョブを再試行するワークフローを設定Graph。 これは、一時的なエラーまたは計画的または計画外の停止が原因でバックアップ ジョブが失敗したシナリオで役立ちます。

作業を開始するには、次の手順に従います。

1. Automation アカウント内に Automation アカウントと新しい PowerShell Runbook を作成します。 [詳細については、こちらを参照してください](../automation/learn/powershell-runbook-managed-identity.md)。

2. Runbook の本文に次のスクリプトを挿入します。 

   このスクリプトでは、ARG クエリを実行して、最近のジョブ エラーが発生したすべての VM の一覧をフェッチし _(startTime_ にフィルターをクエリに追加できます)、VM ごとにオンデマンド バックアップをトリガーします。 同様のクエリを作成して、バックアップ中のすべての SQL データベース、HANA データベース、Azure Files、その他の Azure ワークロードの一覧をフェッチできます。

    ```azurepowershell
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    $connectionResult = Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $connection.TenantID `
    -ApplicationId $connection.ApplicationID `
    -CertificateThumbprint $connection.CertificateThumbprint
    "Login successful.."

    $query = "RecoveryServicesResources
    | where type in~ ('microsoft.recoveryservices/vaults/backupjobs')
    | extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
    | extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
    | extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
    | extend protectedItemName = split(split(properties.backupInstanceId, 'protectedItems')[1],'/')[1]
    | extend vaultId = tostring(split(id, '/backupJobs')[0])
    | extend vaultSub = tostring( split(id, '/')[2])
    | extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'), startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration 
    | where (dataSourceType in~ ('AzureIaasVM'))
    | where jobStatus=='Failed'
    | where operation == 'backup' or operation == 'adhocBackup'
    | project vaultSub, vaultId, protectedItemName, startTime, endTime, jobStatus, operation
    | sort by vaultSub"

    $subscriptions = Get-AzSubscription | foreach {$_.SubscriptionId}
    $result = Search-AzGraph -Subscription $subscriptions -Query $query -First 5
    $result = $result.data
    $prevsub = ""
    foreach($jobresponse in $result)
    {
                if($jobresponse.vaultSub -ne $prevsub)
                {
                            Set-AzContext -SubscriptionId $jobresponse.vaultSub
                            $prevsub = $jobresponse.vaultSub
                }

                $item = Get-AzRecoveryServicesBackupItem -VaultId $jobresponse.vaultId -BackupManagementType AzureVM -WorkloadType AzureVM -Name $jobresponse.protectedItemName

                Backup-AzRecoveryServicesBackupItem -ExpiryDateTimeUTC (get-date).AddDays(10) -Item $item -VaultId $jobresponse.vaultId
    }
    ```

3. Runbook に次のモジュールをインポートして、スクリプトが正しく実行されるようにします: `Az.Accounts``Az.RecoveryServices``Az.Graph` 、、。

   [Runbook にモジュールをインポートする方法について学習します](../automation/shared-resources/modules.md)。

4. [Runbook をスケジュールにリンクして](../automation/shared-resources/schedules.md) 、定期的に自動的に実行されるスクリプトを構成します。

   シナリオのエンドツーエンドの手順については、次のビデオを参照してください。 <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-types"></a>操作の種類別にサポートされる自動化方法

### <a name="azure-vm"></a>Azure VM

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています <br><br> [例を参照してください](./backup-azure-vms-automation.md#back-up-azure-vms)。 | サポートされています <br><br> [例を参照してください](./quick-backup-vm-cli.md#enable-backup-for-an-azure-vm) | サポートされています  <br><br>  [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md)。 | サポートされています  <br><br> [例を参照してください](./backup-azure-auto-enable-backup.md)。 | サポートされています  <br><br> [例を参照してください](./backup-rm-template-samples.md)。 |
| Backup | 選択的ディスク バックアップ | サポートされています  <br><br> [例を参照してください](./selective-disk-backup-restore.md#using-powershell) | サポートされています  <br><br> [例を参照してください](./selective-disk-backup-restore.md#using-azure-cli)。 | サポートされています  <br><br> [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)。 | 該当なし | 該当なし |
| Backup | オンデマンドバックアップの実行 | サポートされています   <br><br> [例を参照してください](./quick-backup-vm-powershell.md#start-a-backup-job)。 | サポートされている -  <br><br> [例を参照してください](./quick-backup-vm-cli.md#start-a-backup-job)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md#trigger-an-on-demand-backup-for-a-protected-azure-vm)。 | 該当なし | 該当なし |
| 復元 | ディスクをプライマリ リージョンに復元する | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#restore-an-azure-vm)。 | サポートされています  <br><br> [例を参照してください](./tutorial-restore-disk.md#restore-a-vm-disk)。 | サポートされています  <br><br> [例を参照してください](./backup-azure-arm-userestapi-restoreazurevms.md)。 | 該当なし | 該当なし |
| 復元 | リージョン間の復元 | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#restore-disks-to-a-secondary-region)。 | サポートされています   <br><br> [例を参照してください](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-restoreazurevms.md#cross-region-restore)。 | 該当なし | 該当なし |
| 復元 | 選択的なディスクの復元 | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#restore-selective-disks)。 | サポートされています   <br><br> [例を参照してください](./selective-disk-backup-restore.md#restore-disks-with-azure-cli)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks-selectively)。 | 該当なし | 該当なし |
| 復元 | 復元されたディスクからの VM の作成 | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#using-managed-identity-to-restore-disks)。 | サポートされています   <br><br> [例を参照してください](./tutorial-restore-disk.md#using-managed-identity-to-restore-disks)。 | サポートされています   <br><br> [例を参照してください](/rest/api/backup/restores/trigger)。 | 該当なし | 該当なし |
| 復元 | ファイルを復元する | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。 | サポートされています   <br><br> [例を参照してください](./tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-restoreazurevms.md#restore-disks)。 | 該当なし | 該当なし |
| 管理する | ジョブの監視 | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)。 | サポートされています   <br><br> [例を参照してください](./tutorial-restore-files.md)。 | 該当なし | 該当なし |
| 管理する | バックアップ ポリシーの変更 | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#monitoring-a-backup-job)。 | サポートされています   <br><br> [例を参照してください](./quick-backup-vm-cli.md#monitor-the-backup-job)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)。 | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#retain-data)。 | サポートされています   <br><br> [例を参照してください](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-but-retain-existing-data)。 | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#delete-backup-data)。 | サポートされています   <br><br> [例を参照してください](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true)。 | サポートされています   <br><br> [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)。 | 該当なし | 該当なし |
| 管理する | Resume protection | サポートされています   <br><br> [例を参照してください](./backup-azure-vms-automation.md#resume-backup)。    | サポートされています    <br><br> [例を参照してください](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion) | 該当なし | 該当なし |

### <a name="sql-in-azure-vm"></a>Azure VM 内の SQL

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#configure-a-backup-policy)。 | サポートされています | サポートされています | 現在はここではありません | サポートされています    <br><br> [例を参照してください](./backup-rm-template-samples.md)。 |
| Backup | 自動保護を有効にする | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#enable-autoprotection) | サポートされています | サポートされています | N/A | 該当なし |
| Backup | オンデマンドバックアップの実行 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#on-demand-backup)。 | サポートされています | サポートされています | N/A | 該当なし |
| 復元 | 個別の完全/差分復旧ポイントへの復元 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#original-restore-with-distinct-recovery-point)。 | サポートされています | サポートされています | N/A | 該当なし |
| 復元 | 特定の時点への復元 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#original-restore-with-log-point-in-time)。 | サポートされています | サポートされています | N/A | 該当なし |
| 復元 | リージョン間の復元 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#alternate-workload-restore-to-a-vault-in-secondary-region)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | ジョブの監視 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#track-azure-backup-jobs)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | アラートAzure Monitor管理 (プレビュー) | サポートされています    <br><br> [例を参照してください](../azure-monitor/powershell-samples.md)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | メトリックAzure Monitor管理 (プレビュー) | サポートされています    <br><br> [例を参照してください](../azure-monitor/powershell-samples.md)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | バックアップ ポリシーの変更 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#change-policy-for-backup-items)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#retain-data)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#delete-backup-data)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | インスタンスの登録解除 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#unregister-sql-vm)。 | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | インスタンスの再登録 | サポートされています    <br><br> [例を参照してください](./backup-azure-sql-automation.md#re-register-sql-vms)。 | サポートされています | サポートされています | N/A | 該当なし |

### <a name="sap-hana-in-azure-vm"></a>Azure VM 内の SAP HANA

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | 以下は現在サポートされていません | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance)。 | サポートされています | 以下は現在サポートされていません | 該当なし |
| Backup | オンデマンドバックアップの実行 | 以下は現在サポートされていません | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-backup-cli.md#trigger-an-on-demand-backup)。 | サポートされています | N/A | サポート – 例   <br><br> [例を参照してください](./backup-rm-template-samples.md)。 |
| 復元 | 個別の完全復旧ポイント、差分復旧ポイント、増分復旧ポイントに復元する | 以下は現在サポートされていません | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-restore-cli.md#restore-a-database)。 | サポートされています | N/A |    |
| 復元 | 特定の時点への復元 | 以下は現在サポートされていません  | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-restore-cli.md#restore-a-database)。 | サポートされています | N/A | 該当なし |
| 復元 | リージョン間の復元 | 以下は現在サポートされていません | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | ジョブの監視 | 以下は現在サポートされていません  | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | バックアップ ポリシーの変更 | 以下は現在サポートされていません | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-manage-cli.md#change-policy)。 | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | 以下は現在サポートされていません  | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-manage-cli.md#stop-protection-with-retain-data) | サポートされています    <br><br> [例を参照してください](./backup-azure-arm-userestapi-createorupdatepolicy.md)。 | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | 以下は現在サポートされていません  | サポートされています    <br><br> [例を参照してください](./tutorial-sap-hana-manage-cli.md#stop-protection-without-retain-data)。 | サポートされています    <br><br> [例を参照してください](/rest/api/backup/protected-items/delete)。 | 該当なし | 該当なし |
| 管理する | インスタンスの登録解除 | 以下は現在サポートされていません  | サポートされています | サポートされています | N/A | 該当なし |
| 管理する | インスタンスの再登録 | 以下は現在サポートされていません  | サポートされています | サポートされています | N/A | なし |

### <a name="azure-files"></a>Azure Files

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています    <br><br> [例を参照してください](./backup-azure-afs-automation.md#configure-a-backup-policy)。 | サポートされています    <br><br> [例を参照してください](./backup-afs-cli.md#enable-backup-for-azure-file-shares)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-file-share-rest-api.md#configure-backup-for-an-unprotected-azure-file-share-using-rest-api)。 | 以下は現在サポートされていません | サポートされています    <br><br> [例を参照してください](./backup-rm-template-samples.md)。 |
| Backup | オンデマンドバックアップの実行 | サポートされています    <br><br> [例を参照してください](./backup-azure-afs-automation.md#trigger-an-on-demand-backup)。 | サポートされています    <br><br> [例を参照してください](./backup-afs-cli.md#trigger-an-on-demand-backup-for-file-share)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share)。 | 該当なし | 該当なし |
| 復元 | 元の場所または別の場所に復元する | サポートされています    <br><br> [例を参照してください](./restore-afs-powershell.md)。 | サポートされています    <br><br> [例を参照してください](./restore-afs-cli.md)。 | サポートされています    <br><br> [例を参照してください](./restore-azure-file-share-rest-api.md)。 | 該当なし | 該当なし |
| 管理する | ジョブの監視 | サポートされています    <br><br> [例を参照してください](./manage-afs-powershell.md#track-backup-and-restore-jobs)。 | サポートされています    <br><br> [例を参照してください](./manage-afs-backup-cli.md#monitor-jobs)。 | サポートされています    <br><br> [例を参照してください](./manage-azure-file-share-rest-api.md#monitor-jobs)。 | 該当なし | 該当なし |
| 管理する | バックアップ ポリシーの変更 | サポートされています    <br><br> [例を参照してください](./manage-afs-powershell.md#modify-the-protection-policy)。 | サポートされています    <br><br> [例を参照してください](./manage-afs-backup-cli.md#modify-policy)。 | サポートされています    <br><br> [例を参照してください](./manage-azure-file-share-rest-api.md#modify-policy)。 | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | サポートされています    <br><br> [例を参照してください](./manage-afs-powershell.md#stop-protection-and-retain-recovery-points)。 | サポートされています    <br><br> [例を参照してください](./manage-afs-backup-cli.md#stop-protection-and-retain-recovery-points)。 | サポートされています    <br><br> [例を参照してください](./manage-azure-file-share-rest-api.md#stop-protection-but-retain-existing-data)。 | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | サポートされています    <br><br> [例を参照してください](./manage-afs-powershell.md#stop-protection-without-retaining-recovery-points)。 | サポートされています    <br><br> [例を参照してください](./manage-afs-backup-cli.md#stop-protection-without-retaining-recovery-points)。 | サポートされています    <br><br> [例を参照してください](./manage-azure-file-share-rest-api.md#stop-protection-and-delete-data)。 | 該当なし | 該当なし |

### <a name="azure-blobs"></a>Azure BLOB

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています    <br><br> [例を参照してください](./backup-blobs-storage-account-ps.md)。 | サポートされています    <br><br> [例を参照してください](./backup-blobs-storage-account-cli.md)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-backup-blobs.md)。 | 以下は現在サポートされていません | サポートされています |
| 復元 | BLOB を復元する | サポートされています    <br><br> [例を参照してください](./restore-blobs-storage-account-ps.md)。 | サポートされています    <br><br> [例を参照してください](./restore-blobs-storage-account-cli.md)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-restore-blobs.md)。 | 該当なし | 該当なし |
| 管理する | ジョブの監視 | サポートされています    <br><br> [例を参照してください](./restore-blobs-storage-account-ps.md#tracking-job)。 | サポートされています    <br><br> [例を参照してください](./restore-blobs-storage-account-cli.md#tracking-job)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-restore-blobs.md#tracking-jobs)。 | 該当なし | 該当なし |
| 管理する | バックアップ ポリシーの変更 | 以下は現在サポートされていません | 以下は現在サポートされていません | 以下は現在サポートされていません | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | 以下は現在サポートされていません | 以下は現在サポートされていません | 以下は現在サポートされていません | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | Resume protection | 以下は現在サポートされていません | 以下は現在サポートされていません | 以下は現在サポートされていません | 該当なし | 該当なし |

### <a name="azure-disks"></a>Azure ディスク

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています   <br><br> [例を参照してください](./backup-managed-disks-ps.md)。 | サポートされています    <br><br> [例を参照してください](./backup-managed-disks-cli.md)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-backup-disks.md)。 | 以下は現在サポートされていません | サポートされています |
| Backup | オンデマンドバックアップの実行 | サポートされています    <br><br> [例を参照してください](./backup-managed-disks-ps.md#run-an-on-demand-backup)。 | サポートされています    <br><br> [例を参照してください](./backup-managed-disks-cli.md#run-an-on-demand-backup)。 |  該当なし | 該当なし |
| 復元 | 新しいディスクに復元する | サポートされています    <br><br> [例を参照してください](./restore-managed-disks-ps.md)。 | サポートされています    <br><br> [例を参照してください](./restore-managed-disks-cli.md)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-restore-disks.md)。 | 該当なし | 該当なし |
| 管理する | ジョブの監視 | サポートされています    <br><br> [例を参照してください](./restore-managed-disks-ps.md#tracking-job)。 | サポートされています    <br><br> [例を参照してください](./restore-managed-disks-cli.md#tracking-job)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-restore-disks.md#track-jobs)。 | 該当なし | 該当なし |
| 管理する | バックアップ ポリシーの変更 | 以下は現在サポートされていません | 以下は現在サポートされていません | 以下は現在サポートされていません | 該当なし | 該当なし |
| 管理する | 保護を停止してバックアップ データを保持する | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してバックアップ データを削除する | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | Resume protection | サポートされています | サポート | サポートされています | N/A | 該当なし |

### <a name="azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバー

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | バックアップポリシーを作成してバックアップを構成する | サポートされています | サポート | サポートされています | 現在はここではありません | サポートされています |
| Backup | オンデマンドバックアップの実行 | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 復元 | ターゲットストレージアカウントにデータベースを復元します | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | バックアップ ポリシーの変更 | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してデータを削除する | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | 保護を停止してデータを保持する | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | Resume protection | サポートされています | サポート | サポートされています | N/A | 該当なし |

### <a name="vault-level-configurations"></a>コンテナーレベルの構成

| **カテゴリ** | **操作** | **PowerShell** | **CLI** | **REST API** | **Azure Policy** | **ARM テンプレート** |
| --- | --- | --- | --- | --- | --- | --- |
| 管理する | Recovery Services コンテナーの作成 | サポートされています    <br><br> [例を参照してください](./backup-azure-vms-automation.md#create-a-recovery-services-vault)。 | サポートされています    <br><br> [例を参照してください](./quick-backup-vm-cli.md#create-a-recovery-services-vault)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-arm-userestapi-createorupdatevault.md)。 | 該当なし | サポートされています    <br><br> [例を参照してください](./backup-rm-template-samples.md)。 |
| 管理する | バックアップ コンテナーの作成 | サポートされています    <br><br> [例を参照してください](./backup-blobs-storage-account-ps.md#create-a-backup-vault)。 | サポートされています    <br><br> [例を参照してください](./backup-blobs-storage-account-cli.md#create-a-backup-vault)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)。 | 該当なし | サポート |
| 管理する | Recovery Services コンテナーを移動する | サポートされています    <br><br> [例を参照してください](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-move-recovery-services-vault.md#use-powershell-to-move-recovery-services-vault)。 | サポートされています | N/A | 該当なし |
| 管理する | バックアップ コンテナーの移動 | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | Recovery Services コンテナーの削除 | サポートされています    <br><br> [例を参照してください](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-powershell)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli)。 | サポートされています    <br><br> [例を参照してください](./backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-azure-resource-manager)。 | 該当なし | 該当なし |
| 管理する | バックアップ コンテナーを削除する | サポートされています | Here | Here | 該当なし | 該当なし |
| 管理する | 診断設定の構成 | サポートされています | サポート | サポート | サポートされています    <br><br> [例を参照してください](./azure-policy-configure-diagnostics.md)。 | サポートされています |
| 管理する | アラートAzure Monitor管理 (プレビュー) | サポートされています | サポート | サポートされています | N/A | 該当なし |
| 管理する | メトリックAzure Monitor管理 (プレビュー) | サポートされています | サポート | サポートされています | N/A | 該当なし |
| セキュリティ | Recovery Services コンテナーのプライベート エンドポイントを有効にする | サポートされています | サポート | サポートされています | 現在サポートされている監査ポリシーのみ | サポートされています |
| セキュリティ | Recovery Services 資格情報コンテナーのユーザーが管理するキーを有効にします。 | サポートされています | サポート | サポートされています | 現在サポートされている監査ポリシーのみ | サポートされています |
| セキュリティ | Recovery Services コンテナーの論理的な削除を有効にする | サポートされています | サポート | サポートされています | 以下は現在サポートされていません | サポートされています |
| 回復性 | Recovery Services コンテナーのリージョン間の復元を有効にする | サポートされています | サポート | サポートされています | 以下は現在サポートされていません | サポートされています |