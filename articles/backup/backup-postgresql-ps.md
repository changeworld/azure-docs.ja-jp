---
title: Azure PowerShell を使用して Azure Database for PostgreSQL を長期保存でバックアップする
description: Azure PowerShell を使用して Azure Database for PostgreSQL をバックアップする方法について説明します。
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d11bebf36de7c5ffd7fae8b774ee428ae3a652b2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714734"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell を使用して Azure PostgreSQL データベースをバックアップする

この記事では、Azure PowerShell を使用して [Azure PostgreSQL データベース](../postgresql/overview.md#azure-database-for-postgresql---single-server)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- Azure PostgreSQL データベースのバックアップを構成する

- オンデマンド バックアップ ジョブを実行する

Azure PostgreSQL データベースでサポートされるシナリオと制限事項については、[サポート マトリックス](backup-azure-database-postgresql-overview.md#support-matrix)に関するページをご覧ください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバー、Azure Disks、および Azure BLOB など、Azure Backup によってサポートされるさまざまな新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

バックアップ コンテナーを作成する前に、コンテナー内のデータのストレージ冗長を選択します。 次に、そのストレージ冗長と場所を使用したバックアップ コンテナーの作成に進みます。

この記事では、リソース グループ *testBkpVaultRG* の *westus* リージョンに、バックアップ コンテナー *TestBkpVault* を作成します。 [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) コマンドを使用してバックアップ コンテナーを作成します。 詳細については、「[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)」を参照してください。

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

コンテナーを作成したら、Azure PostgreSQL データベースを保護するバックアップ ポリシーを作成しましょう。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

### <a name="understand-postgresql-backup-policy"></a>PostgreSQL のバックアップ ポリシーについて

ディスク バックアップは 1 日に複数回のバックアップを行い、BLOB バックアップはトリガーのない "*継続的*" バックアップを行いますが、PostgreSQL のバックアップではアーカイブを保護することができます。 最初にコンテナーに送信されるバックアップ データは、定義された規則や "*ライフサイクル*" に従って、*Archive* レベルに移動することができます。 このコンテキストで、PostgreSQL のバックアップ ポリシー オブジェクトについて理解しましょう。

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (この場合はデータベースの完全バックアップ)
         -  初期データストア (バックアップを最初に保存する場所)
         -  トリガー (バックアップのトリガー方法)
            -  スケジュール ベース
            -  既定のタグ付け条件 (スケジュールされたすべてのバックアップの既定の 'タグ'。 このタグにより、バックアップが保有規則にリンクされます)
   -  既定の保有規則 (初期データストアにおいて、既定ですべてのバックアップに適用される規則)

そのため、このオブジェクトは、トリガーされるバックアップの種類、トリガー方法 (スケジュールを使用)、タグを付ける対象、その保存先 (データストア)、およびデータストア内のバックアップ データのライフサイクルを定義します。 PostgreSQL の既定の PowerShell オブジェクトでは、"*完全*" バックアップを毎週トリガーし、コンテナーに保存され、3 か月間格納されるようになっています。

*Archive* レベルをポリシーに追加する場合は、データをコンテナーからアーカイブに移動するタイミング、データをアーカイブに保存する期間、およびスケジュールされたバックアップの内、どのバックアップに "_アーカイブ可能_" の *タグを付ける* かを決定する必要があります。 そのためには、"*保持規則*" を追加する必要があります。ここでは、コンテナー データストアからアーカイブ データストアへのバックアップ データのライフサイクルと、"*アーカイブ*" データストアに保持する期間を定義します。 次に、スケジュールされたバックアップを "_アーカイブ対象_" としてマークする "*タグ*" を追加する必要があります。

結果の PowerShell オブジェクトは次のようになります。


-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (この場合はデータベースの完全バックアップ)
         -  初期データストア (バックアップを最初に保存する場所)
         -  トリガー (バックアップのトリガー方法)
            -  スケジュール ベース
            -  既定のタグ付け条件 (スケジュールされたすべてのバックアップの既定の 'タグ'。 このタグにより、バックアップが保有規則にリンクされます)
            -  新しい保有規則の新しいタグ付け条件 ('X' と同じ名前)
   -  既定の保有規則 (初期データストアにおいて、既定ですべてのバックアップに適用される規則)
   -  'X' という名前の新しい保有規則
      -  ライフサイクル
         -  ソース データストア
         -  ソース データストアで一定期間が経過した後削除する
         -  ターゲット データストアにコピー

### <a name="retrieving-the-policy-template"></a>ポリシー テンプレートの取得

Azure PostgreSQL データーベース バックアップのバックアップ ポリシーの内部構成要素を理解するには、[Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDatabaseForPostgreSQL
$policyDefn | fl


DatasourceType : {Microsoft.DBforPostgreSQL/servers/databases}
ObjectType     : BackupPolicy
PolicyRule     : {BackupWeekly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : VaultStore
Name                      : BackupWeekly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.ScheduleBasedTriggerCo
                            ntext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

ポリシー テンプレートは、トリガー (何がバックアップをトリガーするかを決定) とライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) で構成されます。 Azure PostgreSQL データベース バックアップでは、トリガーの既定値はスケジュールされた週単位のトリガー (7 日に 1 回のバックアップ) で、各バックアップは 3 か月間保持されます。

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2021-08-22T02:00:00+00:00/P1W}
ScheduleTimeZone              : UTC
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P3M
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : VaultStore
TargetDataStoreCopySetting : {}
```

### <a name="modify-the-policy-template"></a>ポリシー テンプレートを変更する

#### <a name="modify-the-schedule"></a>スケジュールを変更する

既定のポリシー テンプレートでは、バックアップは週に 1 回行われます。 このスケジュールは、バックアップを週に複数日行うように変更することができます。 スケジュールを変更するには、[Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true) コマンドを使用します。

次の例では、毎週日曜日、水曜日、および金曜日にバックアップが行われるように、週単位のバックアップを変更しています。 スケジュールの日付配列は日付を表し、その日付の曜日は曜日として扱われます。 また、これらのスケジュールが毎週繰り返されるように指定する必要があります。 そのため、スケジュールの間隔は "1"、間隔の種類は "Weekly " となっています。

```azurepowershell-interactive
$schDates = @(
    (
        (Get-Date -Year 2021 -Month 08 -Day 15 -Hour 22 -Minute 0 -Second 0)
    ), 
    (
        (Get-Date -Year 2021 -Month 08 -Day 18 -Hour 22 -Minute 0 -Second 0)
    ),
  (
        (Get-Date -Year 2021 -Month 08 -Day 20 -Hour 22 -Minute 0 -Second 0)
    )
)
$trigger = New-AzDataProtectionPolicyTriggerScheduleClientObject -ScheduleDays $schDates -IntervalType Weekly -IntervalCount 1 
Edit-AzDataProtectionPolicyTriggerClientObject -Schedule $trigger -Policy $policyDefn   
```

#### <a name="add-a-new-retention-rule"></a>新しい保持規則を追加する

そのため、"_アーカイブ_" 保護を追加したい場合は、ポリシー テンプレートを以下のように変更する必要があります。

既定のテンプレートには、既定の保持規則のもと、初期データストアのライフサイクルが設定されています。 このシナリオでは、3 か月後にバックアップ データを削除する規則となっています。 データを "*アーカイブ*" データストアに "*移動*" する (つまり、バックアップ データを最初にアーカイブ データストアにコピーした後にコンテナー データストアで削除する) タイミングを定義する新しい保有規則を追加する必要があります。 また、この規則では、データを "*アーカイブ*" データストアに保持する期間も定義します。 [New-AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true) コマンドを使用して新しいライフサイクルを作成した後、[Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true) コマンドを使用して、それらを新しい規則または既存の規則に関連付けます。

次の例では、毎月最初に成功したバックアップを 6 か月間コンテナーに保持し、その後 Archive レベルに移動して 24 か月間 Archive レベルに保持する、*Monthly* という名前の新しい保持規則を作成します。

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>タグと関連条件を追加する

保持規則を作成したら、バックアップ ポリシーの *Trigger* プロパティに、対応する "*タグ*" を作成する必要があります。 [New-AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true) コマンドを使用して新しいタグ付け条件を作成し、[Edit-AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true) コマンドを使用して既存のタグを更新するか、新しいタグを作成します。

次の例では、適用予定の対応する保持規則と同じ名前の基準 (月の最初に成功したバックアップ) とともに、新しい "*タグ*" を作成します。

この例の場合、タグ付けの条件の名前は *Monthly* である必要があります。

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

週に複数回 (上記の例では毎週日曜日、水曜日、木曜日) バックアップするようにスケジュールが設定されている状態で、日曜日と金曜日のバックアップをアーカイブしたい場合は、タグ付けの基準を以下のように変更します。

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>新しい PostgreSQL バックアップ ポリシーを作成する

要件に従ってテンプレートを変更したら、[New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true) コマンドを使用して、変更したテンプレートを使用してポリシーを作成します。

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、Azure PostgreSQL データベースを保護するために考慮する必要がある重要なポイントが 3 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="postgresql-database-to-be-protected"></a>保護する PostgreSQL データベース

保護する PostgreSQL の Azure Resource Manager ID (ARM ID) を取得します。 これは、データベースの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ **ossrg** に存在する PostgreSQL サーバー **testposgresql** の下に、**empdb11** という名前のデータベースがある場合を例を使用します。

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup サービスには、PostgreSQL データベースに接続するためのユーザー名とパスワードは保存されません。 代わりに、バックアップ管理者がキー コンテナーに "*キー*" をシードすると、バックアップ サービスがキー コンテナーにアクセスしてキーを読み取り、データベースにアクセスします。 関連するキーのシークレット識別子をメモします。

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

ユーザーは、バックアップ コンテナーを PostgreSQL サーバーに接続してから、キー コンテナーに存在するキーを使用してデータベースにアクセスする必要があります。 そのため、PostgGreSQL サーバーとキー コンテナーへのアクセスが必要となります。 アクセス許可は、バックアップ コンテナーの MSI に付与されます。

データベースのキーが保存されている PostgreSQL サーバー上のバックアップ コンテナーの MSI と、Azure キー コンテナーに付与すべき[適切な権限について確認します](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。

1. [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用して、関連するコンテナー、ポリシー、PostgreSQL データベースを使用し、関連する要求を準備します。
1. [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用してデータベースを保護する要求を送信します。

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップをトリガーする必要がある関連するバックアップ インスタンスを、[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用して取り込みます。

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

バックアップのトリガー中に、保持ルールを指定できます。 ポリシーの保持ルールを表示するには、ポリシー オブジェクト内を移動して保持ルールを探します。 次の例では、*default* という名前の規則が示されています。 この規則は、オンデマンド バックアップに使用します。

```azurepowershell-interactive
$ossPol.PolicyRule | fl


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

## <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

オンデマンド バックアップをトリガーするには、[Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>ジョブを追跡する

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

_Az.ResourceGraph_ を使用して、すべてのバックアップ コンテナーにわたるすべてのジョブを追跡することもできます。 [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用して、任意のバックアップ コンテナー全体にまたがって関連するジョブを取得します。

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>次の手順

- [Azure PowerShell を使用して Azure PostgreSQL データベースを復元する](restore-managed-disks-ps.md)