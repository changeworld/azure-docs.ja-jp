<properties
	pageTitle="PowerShell を使用した Azure VM のバックアップのデプロイおよび管理 | Microsoft Azure"
	description="PowerShell を使用して Microsoft Azure Backup をデプロイおよび管理する手順の説明"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/21/2015" ms.author="trinadhk";"aashishr" />


# PowerShell を使用した Azure VM のバックアップのデプロイおよび管理
この記事では、Azure の IaaS の VM をバックアップおよび回復するために Azure PowerShell を使用する方法を示します。

## 概念
Azure Backup のドキュメント「[Azure の IaaS の VM のバックアップの概要](backup-azure-vms-introduction.md)」を参照してください。ここでは、VM をバックアップする理由、前提条件および制限事項の概要を説明しています。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![オブジェクト階層](./media/backup-azure-vms-automation/object-hierarchy.png)

2 つの最も重要なフローは、VM の保護の有効化と、復旧ポイントからのデータの復元です。この記事は、これらの 2 つのシナリオを実現するために、PowerShell コマンドレットの操作に熟達することを目的としています。


## セットアップと登録
開始するには

1. [最新の PowerShell](https://github.com/Azure/azure-powershell/releases) (0.9.8 以降のバージョンが必要) をダウンロードします。

2. **Switch-azuremode** コマンドレットを使用して、*AzureResourceManager* モードに切り替えて Azure Backup コマンドレットを使用可能にします。

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell を使用して次のセットアップおよび登録タスクを自動化できます。

- バックアップ コンテナーを作成していること
- Microsoft Azure Backup サービスを使用した VM の登録

### バックアップ コンテナーを作成していること

> [AZURE.WARNING]顧客が初めて Azure Backup を使用する場合、サブスクリプションで使用する Azure Backup プロバイダーを登録する必要があります。これは、Register-AzureProvider -ProviderNamespace "Microsoft.Backup" コマンドを実行して行うことができます。

**New-AzureRMBackupVault** コマンドレットを使用すると、新しいバックアップ コンテナーを作成できます。バックアップ コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。管理者特権の Azure PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> New-AzureResourceGroup –Name “test-rg” –Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

**Get-AzureRMBackupVault** コマンドレットを使用して、特定のサブスクリプション内のすべてのバックアップ コンテナーの一覧を取得できます。

> [AZURE.NOTE]バックアップ コンテナー オブジェクトは変数に格納すると便利です。コンテナー オブジェクトは、多くの Azure Backup コマンドレットの入力に必要です。


### VM の登録
Azure Backup のバックアップを構成するには、まず Azure Backup コンテナーにコンピューターまたは VM を登録します。**Register-AzureRMBackupContainer** コマンドレットは、Azure IaaS 仮想マシンの入力情報を受け取り、指定のコンテナーに登録を行います。登録操作は、Azure virtual machine とバックアップ コンテナーを関連付け、VM をバックアップのライフサイクルを通じて追跡します。

Azure Backup サービスを VM に登録する場合、最上位のコンテナー オブジェクトが作成されます。通常コンテナーには、バックアップ可能な項目が複数ありますが、VM の場合、コンテナーのバックアップ項目は 1 つのみとなります。

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Azure VM のバックアップ

### 保護ポリシーの作成
VM のバックアップを開始する際、新しい保護ポリシーを作成する必要はありません。コンテナーには、後ですぐに正しい詳細に編集できる、保護をすぐに有効にするために使用できる '既定のポリシー' が含まれます。**Get-AzureRMBackupProtectionPolicy** コマンドレットを使用すると、コンテナーにあるポリシーの一覧を取得することができます。

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。アイテム保持ポリシーには、Azure Backup で復旧ポイントを保持する期間が定義されています。**New-AzureRMBackupRetentionPolicy** コマンドレットは、アイテム保持ポリシー情報を保持する PowerShell オブジェクトを作成します。これらのアイテム保持ポリシー オブジェクトは、*New-AzureRMBackupProtectionPolicy* コマンドレットへの入力、または *Enable-AzureRMBackupProtection* コマンドレットと同時に直接使用されます。

バックアップ ポリシーには、アイテムのバックアップが実行されるタイミングと方法を定義します。**New-AzureRMBackupProtectionPolicy** コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。バックアップ ポリシーは、*Enable-AzureRMBackupProtection* コマンドレットへの入力として使用されます。

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### 保護を有効にする
保護を有効にするには、同じコンテナーに属する、アイテムとポリシーの 2 つのオブジェクトが必要です。ポリシーとアイテムの関連付けが完了すると、バックアップのワークフローが定義されたスケジュールで開始されます。

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### 初回バックアップ
バックアップのスケジュールによって、アイテムの完全な初回コピーと以降のバックアップの増分コピーが行われます。ただし、初回バックアップを特定の時間に強制的に行う場合や直ちに行う場合は、**Backup-AzureRMBackupItem** コマンドレットを使用します。

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

### バックアップ ジョブの監視
Azure Backup で長時間実行される多くの操作は、ジョブとしてモデル化されています。これにより、Azure ポータルを常に開いていなくても進捗を簡単に追跡できるようになります。

進行中のジョブの最新の状態を得るには、**Get-AzureRMBackupJob** コマンドレットを使用します。

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

これらのジョブが完了するまで不要にポーリングするのではなく、別の **Wait-AzureRMBackupJob** コマンドレットを使用する方が簡単になります。スクリプトで使用する場合、ジョブが完了するまで、または指定したタイムアウト値に到達するまでコマンドレットは実行を停止します。

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## Azure VM の復元

バックアップ データを復元するには、バックアップ項目と復元する特定の時点のデータを保持する復元ポイントを特定する必要があります。この情報は、コンテナーから顧客のアカウントにデータの復元を開始するために、Restore-AzureRMBackupItem コマンドレットに提供されます。

### VM の選択

バックアップする正しい項目を特定する PowerShell オブジェクトを取得するには、コンテナー内のコンテナーから開始し、次いでオブジェクト階層の上から下に進みます。VM のコンテナーを選択するには、**Get-AzureRMBackupContainer** コマンドレットを使用し、**Get AzureRMBackupItem** コマンドレットへのパイプとして使用します。

```
PS C:\> $backupitem = Get-AzureRMBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### 復元ポイントの選択

これで **Get-AzureRMBackupRecoveryPoint** コマンドレットを使用して、バックアップ アイテムのすべての復元ポイントを一覧表示して、復元する復元ポイントを選択できます。通常、ユーザーが一覧表示内の最新 *AppConsistent* ポイントを選択します。

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

### データの復元

Azure ポータルと Azure PowerShell を使用して実行する復元処理には決定的な違いがあります。Powershell では、復元操作は、復元ポイントからディスクと構成情報を復元すると停止します。仮想マシンは作成されません。

> [AZURE.WARNING]Restore-AzureRMBackupItem では、VM は作成されません。指定したストレージ アカウントにディスクを復元するのみです。これは、Azure ポータルの動作とは異なります。

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

復元ジョブが一度完了すると、復元操作の詳細は、**Get-AzureRMBackupJobDetails** コマンドレットを使用して取得できます。*ErrorDetails* プロパティには、VM を再構築するために必要な情報が含まれます。

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### VM の構築

復元したディスクから VM を構築するには、Azure の古い ServiceManager PowerShell コマンドレット、新しい Azure ResourceManager テンプレート、または Azure ポータルを使用して実行できます。Azure ServiceManager コマンドレットを使用して、これを実行する簡単な例を示します。

```
 $properties  = $details.Properties

 $storageAccountName = $properties["TargetStorageAccountName"]
 $containerName = $properties["TargetContainerName"]
 $blobName = $properties["TargetBlobName"]

 Switch-AzureMode AzureServiceManagement

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


 $obj = [xml](Get-Content $destination_path)
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0;
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

復元したディスクから VM を構築する方法の詳細については、次のコマンドレットに関する説明を参照してください。

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

<!---HONumber=Sept15_HO4-->