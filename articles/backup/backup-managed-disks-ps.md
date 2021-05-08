---
title: Azure PowerShell を使用した Azure マネージド ディスクのバックアップ
description: Azure PowerShell を使用して Azure マネージド ディスクをバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629765"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell を使用した Azure マネージド ディスクのバックアップ

この記事では、Azure PowerShell を使用して [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- Azure ディスクのバックアップを構成する

- オンデマンド バックアップ ジョブを実行する

Azure ディスク バックアップの使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバーや Azure ディスクなど、Azure Backup によってサポートされるさまざまな新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

バックアップ コンテナーを作成する前に、コンテナー内のデータのストレージ冗長を選択します。 次に、そのストレージ冗長と場所を使用したバックアップ コンテナーの作成に進みます。 この記事では、リソース グループ "testBkpVaultRG" の "westus" リージョンに、バックアップ コンテナー "TestBkpVault" を作成します。 [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) コマンドを使用してバックアップ コンテナーを作成します。詳細については、[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)に関する記事を参照してください。

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

コンテナーを作成した後は、Azure ディスクを保護するためのバックアップ ポリシーを作成しましょう。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

Azure ディスク バックアップのバックアップ ポリシーの内部構成要素を理解するには、[Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

ポリシー テンプレートは、トリガー (何がバックアップをトリガーするかを決定) とライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) で構成されます。 Azure ディスク バックアップでは、トリガーの既定値は、4 時間ごとにスケジュールされた時間単位のトリガー (PT4H) と、各バックアップを 7 日間保持することです。

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure ディスク バックアップでは、1 日に複数のバックアップを作成できます。 頻繁にバックアップを行う必要がある場合は、4、6、8、または 12 時間の間隔でバックアップを作成できる **[時間単位]** のバックアップ頻度を選択します。 **[時間]** で選択した間隔に基づいてバックアップがスケジュールされます。 たとえば、 **[4 時間ごと]** を選択した場合、ほぼ 4 時間ごとにバックアップが作成されるので、バックアップが 1 日のうちで均等に分散されます。 1 日 1 回のバックアップで十分な場合は、 **[日単位]** のバックアップ頻度を選択します。 日単位のバックアップ頻度では、バックアップを作成する時刻を指定できます。 この時刻は、バックアップが完了する時刻ではなく、バックアップの開始時刻を示していることに注意してください。 バックアップ操作を完了するために必要な時間は、ディスクのサイズや連続するバックアップ間のチャーン率など、さまざまな要因によって異なります。 ただし、Azure ディスク バックアップは[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用するエージェントレス バックアップであり、実稼働アプリのパフォーマンスには影響しません。

   >[!NOTE]
   > 選択したコンテナーにグローバル冗長の設定が含まれる場合がありますが、Azure ディスク バックアップでは現在スナップショット データストアのみがサポートされています。 すべてのバックアップがサブスクリプションのリソース グループに格納され、バックアップ コンテナーのストレージにはコピーされません。

ポリシー作成の詳細については、[Azure ディスク バックアップ ポリシー](backup-managed-disks.md#create-backup-policy)に関するドキュメントを参照してください。

時間単位の頻度または保持期間を編集する場合は、[Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) や [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) コマンドを使用します。 ポリシー オブジェクトに目的の値をすべて設定したら、[New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true) を使用したポリシー オブジェクトからの新しいポリシーの作成に進みます。

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、Azure ディスクを保護するためにユーザーが考慮する必要がある重要なポイントが 3 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="disk-to-be-protected"></a>保護するディスク

保護するディスクの ARM ID を取り込みます。 これは、ディスクの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ "diskrg" の下にある "PSTestDisk" という名前のディスクの例を使用します。

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>スナップショット リソース グループ

ディスクのスナップショットは、サブスクリプション内のリソース グループに格納されます。 ガイドラインとして、Azure Backup サービスで使用されるスナップショット データストアとして専用のリソース グループを作成することをお勧めします。 専用のリソース グループを使用すると、リソース グループに対するアクセス許可を制限できるため、バックアップ データを安全かつ簡単に管理できます。 ディスクのスナップショットを配置するリソース グループの ARM ID をメモしておきます。

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーには、ディスクに対するアクセス許可と、スナップショットをトリガーしてそのライフサイクルを管理できるようにするためのスナップショット リソース グループが必要です。 コンテナーのシステム割り当てマネージド ID は、そのようなアクセス許可を割り当てるために使用されます。 [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) コマンドを使用して、Recovery Services コンテナーのシステム割り当てマネージド ID を有効にします。

### <a name="assign-permissions"></a>アクセス許可の割り当て

ユーザーは、コンテナー (コンテナー MSI で表示) および関連するディスクやディスク RG に対していくつかのアクセス許可を RBAC を介して割り当てる必要があります。 これらは、ポータルまたは PowerShell を使用して実行できます。 関連するすべてのアクセス許可については、[このセクション](backup-managed-disks.md#configure-backup)のポイント 1、2、3 で詳しく説明しています。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。 最初に、[Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用して、関連するコンテナー、ポリシー、ディスク、スナップショット リソース グループを使用し、関連する要求を準備します。 次に、[New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用してディスクを保護する要求を送信します。

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

ユーザーがバックアップをトリガーする必要がある関連するバックアップ インスタンスを、[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) を使用して取り込みます。

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

バックアップのトリガー中に、保持ルールを指定できます。 ポリシーの保持ルールを表示するには、ポリシー オブジェクト内を移動して保持ルールを探します。 下の例では、"Default" という名前のルールが表示されています。このルールをオンデマンド バックアップに使用します。

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

[Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) コマンドを使用して、オンデマンド バックアップをトリガーします。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>ジョブの追跡

すべてのジョブを追跡するには、[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) コマンドを使用します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

Az.ResourceGraph を使用して、すべてのバックアップ コンテナーにわたるすべてのジョブを追跡することもできます。 関連するジョブを取得するには、[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用します。これを、バックアップ コンテナー全体にまたがって行うことも可能です。

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用した Azure マネージド ディスクの復元](restore-managed-disks-ps.md)
