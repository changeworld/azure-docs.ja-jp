<properties
   pageTitle="PowerShell を使用した ARM VM のバックアップのデプロイおよび管理 | Microsoft Azure"
   description="PowerShell を使用した Azure での ARM VM のバックアップのデプロイおよび管理"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# PowerShell を使用した ARM VM のバックアップのデプロイおよび管理

> [AZURE.SELECTOR]
- [Resource Manager の PowerShell](backup-azure-vms-automation.md)
- [クラシック PowerShell](backup-azure-vms-classic-automation.md)

この記事では、Azure PowerShell を使用して Recovery Services コンテナーに Azure 仮想マシン (VM) をバックアップする方法および Recovery Services コンテナーから Azure 仮想マシンを回復する方法について説明します。Recovery Services コンテナーは、Azure Resource Manager (ARM) のリソースであり、Azure Backup サービスと Azure Site Recovery サービスの両方でデータと資産を保護するために使用されます。ARM デプロイで作業する場合は、Recovery Services コンテナーを使用します。Recovery Services コンテナーを使用すると、ARM VM と同様に、Azure Service Manager (ASM) でデプロイされた VM も保護できます。

>[AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。この記事では、Resource Manager モデルで作成された VM を対象とします。

PowerShell を使用した VM の保護および復旧ポイントからデータの復元について説明します。

## 概念

Azure Backup サービスに関する知識が十分でない場合は、「[Azure Backup とは](backup-introduction-to-azure-backup.md)」を参照してください。 開始する前に、Azure Backup を使用するのに必要な前提条件が満たされていることを確認し、現在の VM バックアップ ソリューションの制限事項を把握してください。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## セットアップと登録

開始するには

1. [PowerShell の最新バージョンのダウンロード](https://github.com/Azure/azure-powershell/releases) (バージョン 1.0.0 以降が必要)

2. 以下のコマンドを入力して、使用可能な Azure Backup の PowerShell コマンドレットを検索します。

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


次のタスクは PowerShell を使用して自動化することができます。

- Recovery Services コンテナーを作成する
- Azure VM をバックアップまたは保護する
- バックアップ ジョブをトリガーする
- バックアップ ジョブを監視する
- Azure VM の復元

## Recovery Services コンテナーを作成する

> [AZURE.TIP] Azure Backup を初めて使用する顧客については、使用される Azure Recovery Service プロバイダーをサブスクリプションに登録する必要があります。その登録を行うには、Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices" コマンドを実行します。

新しい Recovery Services コンテナーは、**New-AzureRmRecoveryServicesVault** コマンドレットを使用して作成できます。Recovery Services コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。管理者特権の Azure PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

サブスクリプションに含まれるすべての Recovery Services コンテナーを一覧を表示するには、**Get AzureRmRecoveryServicesVault** コマンドレットを使用します。

### ストレージ冗長性の設定
Recovery Services コンテナーを作成する場合は、使用するストレージ冗長性の種類として、ローカル冗長ストレージ (LRS) または Geo 冗長ストレージ (GRS) のいずれかを指定します。次に示す例では、testVault の BackupStorageRedundancy オプションが GeoRedundant に設定されています。

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。このため、バックアップ Recovery Services コンテナー オブジェクトを変数に格納すると便利です。


## Azure VM のバックアップ

VM に対する保護を有効にする前に、資格情報コンテナーのコンテキストを設定する必要があります。設定したコンテキストは、この後のすべてのコマンドレットに適用されます。

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### 保護ポリシーの作成

新しい資格情報コンテナーを作成すると、これには既定のポリシーが付属します。このポリシーは、毎日午後 9 時 30 分にバックアップ ジョブをトリガーします。バックアップ スナップショットは 30 日間が保持されます。既定のポリシーを使用すると、VM を迅速に保護することができ、後で異なる詳細な内容にポリシーを編集することもできます。

資格情報コンテナー内の使用可能なポリシーの一覧を表示するには、Get-AzureRmRecoveryServicesBackupProtectionPolicy コマンドレットを使用します。

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] PowerShell の BackupTime フィールドのタイムゾーンは UTC です。ただし、Azure ポータルにバックアップ時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。

バックアップ保護ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられます。アイテム保持ポリシーには、Azure Backup で復旧ポイントを保持する期間が定義されています。既定のアイテム保持ポリシーを表示するには、Get-AzureRmRecoveryServicesBackupRetentionPolicyObject を使用します。同様に Get-AzureRmRecoveryServicesBackupSchedulePolicyObject を使用して、既定のスケジュール ポリシーを取得できます。スケジュール ポリシーおよびアイテム保持ポリシー オブジェクトは、New-AzureRmRecoveryServicesBackupProtectionPolicy コマンドレットの入力として使用されます。

バックアップ保護ポリシーには、アイテムのバックアップが実行されるタイミングと方法を定義します。New-AzureRmRecoveryServicesBackupProtectionPolicy コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。バックアップ ポリシーは、Enable-AzureRmRecoveryServicesBackupProtection コマンドレットへの入力として使用されます。

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### 保護を有効にする

保護を有効にするには、アイテムとポリシーの 2 つのオブジェクトが必要です。資格情報コンテナーでの保護を有効にするには両方のオブジェクトが必要です。ポリシーがアイテムに関連付けられると、ポリシーのスケジュールに定義された時刻にバックアップのワークフローが開始されます。

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

ASM ベースの VM の場合

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### 保護ポリシーの変更

ポリシーを変更する場合は、BackupSchedulePolicyObject または BackupRetentionPolicy オブジェクトを変更し、Set-AzureRmRecoveryServicesBackupProtectionPolicy を使用してポリシーを変更します。

次の例では、保持期間を 365 に変更します。

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## 初回バックアップの実行

バックアップのスケジュールでは、アイテムの初回のバックアップ時に完全なバックアップをトリガーします。以後のバックアップでは、バックアップは増分コピーとなります。初回バックアップを強制的に特定の時刻またはすぐに行う場合は、Backup-AzureRmRecoveryServicesBackupItem コマンドレットを使用します。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> PowerShell の StartTime フィールドと EndTime フィールドのタイムゾーンは UTC です。ただし、Azure ポータルに時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。

## バックアップ ジョブの監視

Azure Backup で長時間実行される多くの操作は、ジョブとしてモデル化されています。これにより、Azure ポータルを常に開いていなくても進捗を簡単に追跡できるようになります。

進行中のジョブの最新の状態を取得するには、Get-AzureRMRecoveryservicesBackupJob コマンドレットを使用します。

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

完了確認のためにこれらのジョブをポーリングすると、不要な追加コードが発生します。代わりに、Wait-AzureRmRecoveryServicesBackupJob コマンドレットを使用する方が簡単です。スクリプトで使用する場合、ジョブが完了するまで、または指定したタイムアウト値に到達するまでコマンドレットは実行を停止します。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Azure VM の復元

バックアップ データを復元するには、バックアップされているアイテムと、復元する特定の時点のデータを保持する復旧ポイントを特定する必要があります。この情報を Restore-AzureRMRecoveryServicesBackupItem コマンドレットに指定して、資格情報コンテナーからお客様のアカウントへのデータの復元を開始します。

### VM の選択

バックアップする正しいアイテムを特定する PowerShell オブジェクトを取得するには、資格情報コンテナー内のコンテナーから開始し、オブジェクト階層の上から下に進みます。VM を表すコンテナーを選択するには、Get-AzureRmRecoveryServicesBackupContainer コマンドレットを使用し、Get-AzureRmRecoveryServicesBackupItem コマンドレットへのパイプとして使用します。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### 復元ポイントの選択

これで、Get-AzureRMRecoveryServicesBackupRecoveryPoint コマンドレットを使用して、バックアップ アイテムのすべての復旧ポイントを一覧表示し、復元する復旧ポイントを選択できるようになりました。通常、ユーザーはこのリスト内の最新の AppConsistent ポイントを選択します。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

$rp 変数は、選択したバックアップ アイテムの復旧ポイントの配列であり、時間の逆順に並べ替えられています。つまり、最新の復旧ポイントがインデックス 0 の位置になります。標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。例: $rp[0] は、最新の復旧ポイントを選択します。

### データの復元

Azure ポータルと Azure PowerShell を使用して実行する復元処理には決定的な違いがあります。PowerShell の場合、復旧ポイントからのディスクおよび構成情報の復元時には、復元操作が停止します。仮想マシンは作成されません。

> [AZURE.WARNING] AzureRMRecoveryServicesBackupItem では、VM の作成は行わず、指定されたストレージ アカウントにディスクを復元するのみです。これは、Azure ポータルで実行される内容と異なります。

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

復元ジョブが完了したら、Get-AzureRmRecoveryServicesBackupJobDetails コマンドレットを使用して復元操作の詳細を取得できます。JobDetails プロパティには、VM を再構築するために必要な情報が含まれます。

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Recovery Services コンテナーへの Windows Server または DPM の登録

Recovery Services コンテナーを作成したら、最新のエージェントとコンテナーの資格情報をダウンロードし、C:\\Downloads のような便利な場所に保管します。

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Windows Server または DPM サーバーで、[Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) コマンドレットを実行し、コンピューターを資格情報コンテナーに登録します。

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->