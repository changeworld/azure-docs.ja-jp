---
title: PowerShell を使用して Azure ファイル共有のバックアップをデプロイおよび管理する
description: PowerShell を使用して Azure で Azure ファイル共有のバックアップをデプロイおよび管理する
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell; Azure でのファイル バックアップ; Azure でのファイル復元;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 912336d697e8f7b5d9c71080ec9a052ca562da4b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101144"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>PowerShell を使用して Azure ファイル共有をバックアップおよび復元する

この記事では、Azure PowerShell コマンドレットを使用して、Recovery Services コンテナーから Azure ファイル共有をバックアップする方法と復元する方法を説明します。 Recovery Services コンテナーは、Azure Backup と Azure Site Recovery でデータと資産を保護するために使用される Azure Resource Manager のリソースです。

## <a name="concepts"></a>概念

Azure Backup に関する知識が十分でない場合は、[Azure Backup の概要](backup-introduction-to-azure-backup.md)に関するページを参照してください。 開始する前に、「[Azure ファイル共有のバックアップ](backup-azure-files.md)」に記載されている、Azure ファイル共有のバックアップに使用されるプレビュー機能を参照してください。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

**AzureRm.RecoveryServices.Backup** PowerShell コマンドレット リファレンスを確認するには、Azure ライブラリの [Azure Backup - Recovery Services コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)に関するページを参照してください。

## <a name="setup-and-registration"></a>セットアップと登録

> [!NOTE]
> 「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)」に記載されているように、AzureRM モジュールの新機能のサポートは 2018 年 11 月に終了します。 一般公開になった新しい Az PowerShell モジュールでは、Azure ファイル共有のバックアップのサポートが提供されます。

開始するには、次の手順に従います。

1. [最新バージョンの Az PowerShell をダウンロードします](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0)。 必要な最小バージョンは 1.0.0 です。

2. 以下のコマンドを入力して、使用可能な **Azure Backup PowerShell** コマンドレットを検索します。

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Azure Backup、Azure Site Recovery、Recovery Services コンテナーのエイリアスとコマンドレットが表示されます。 次の画像は表示例です。 コマンドレットの完全な一覧ではありません。

    ![Recovery Services コマンドレットの一覧](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. **Connect-AzAccount** を使用して Azure アカウントにサインインします。 このコマンドレットを実行すると、アカウントの資格情報を入力する Web ページが表示されます。

    * または、**-Credential** パラメーターを使用して、**Connect-AzAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含められます。
    * CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当するテナント ID またはテナントのプライマリ ドメイン名で指定してください。 たとえば、「**Connect-AzAccount -Tenant** fabrikam.com」を指定します。

4. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 次のコマンドを使用して、プロバイダーが正しく登録されたことを確認します。
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    コマンドの出力では、**RegistrationState** が **Registered** に変更されます。 この変更が行われない場合は、**Register-AzResourceProvider** コマンドレットをもう一度実行してください。

次のタスクは PowerShell を使用して自動化することができます。

* Recovery Services コンテナーを作成します。
* Azure ファイル共有のバックアップを構成する。
* バックアップ ジョブをトリガーする。
* バックアップ ジョブを監視する。
* Azure ファイル共有を復元する。
* Azure ファイル共有から個々の Azure ファイルを復元する。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーを作成するには、次の手順に従います。

1. Recovery Services コンテナーは Resource Manager のリソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**New-AzResourceGroup** コマンドレットを使ってリソース グループを作成することもできます。 リソース グループを作成するときは、リソース グループの名前と場所を指定します。  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. **New-AzRecoveryServicesVault** コマンドレットを使用して Recovery Services コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 使用するストレージ冗長性の種類を指定します。 [ローカル冗長ストレージ](../storage/common/storage-redundancy-lrs.md)または [geo 冗長ストレージ](../storage/common/storage-redundancy-grs.md)を使用できます。 次に示す例では、**testvault** の **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、**Get-AzRecoveryServicesVault** を使用します。

```powershell
Get-AzRecoveryServicesVault
```

出力は次の例のようになります。 関連する **ResourceGroupName** と **Location** が指定されていることに注目してください。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。

**Set-AzRecoveryServicesVaultContext** を使用して、コンテナーのコンテキストを設定します。 コンテナーのコンテキストを設定すると、後続のすべてのコマンドレットに適用されます。 次の例では、**testvault** のコンテナーのコンテキストを設定します。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> コンテナーのコンテキストの設定は、Azure PowerShell のガイドラインに従って廃止される予定です。 代わりに、次の手順で説明するようにコンテナー ID を渡すことをお勧めします。

または、PowerShell 操作の実行対象にするコンテナーの ID の格納/フェッチを実行し、それを関連するコマンドに渡します。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Azure ファイル共有のバックアップを構成する

### <a name="create-a-protection-policy"></a>保護ポリシーの作成

バックアップ保護ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられます。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間が定義されています。 既定のアイテム保持ポリシーを表示するには、**Get-AzRecoveryServicesBackupRetentionPolicyObject** を使用します。 

同様に **Get-AzRecoveryServicesBackupSchedulePolicyObject** を使用して、既定のスケジュール ポリシーを取得できます。 **New-AzRecoveryServicesBackupProtectionPolicy** コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。 スケジュール ポリシーとアイテム保持ポリシー オブジェクトは、**New-AzRecoveryServicesBackupProtectionPolicy** コマンドレットへの入力として使用されます。 

次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 この例では、それらの変数を使用して、**NewPolicy** 保護ポリシーの作成時にパラメーターを定義します。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

出力は次の例のようになります。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

**NewAFSPolicy** は毎日のバックアップを受け取り、30 日間保持します。

### <a name="enable-protection"></a>保護を有効にする

保護ポリシーを定義したら、このポリシーを使用して、Azure ファイル共有の保護を有効にできます。

最初に、**Get-AzRecoveryServicesBackupProtectionPolicy** コマンドレットを使用して、関連するポリシー オブジェクトをフェッチします。 このコマンドレットを使用して、特定のポリシーを取得したり、ワークロードの種類に関連付けられているポリシーを表示したりできます。

次の例では、ワークロードの種類が **AzureFiles** であるポリシーを取得します。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

出力は次の例のようになります。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell の **BackupTime** フィールドのタイム ゾーンは協定世界時 (UTC) です。 Azure portal 上でバックアップ時刻が表示されるときに、時刻はローカル タイムゾーンに調整されます。
>
>

次のポリシーでは、**dailyafs** という名前のバックアップ ポリシーを取得します。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

**Enable-AzRecoveryServicesBackupProtection** を使用して、指定のポリシーによる項目の保護を有効にします。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールで定義された時刻にバックアップのワークフローがトリガーされます。

次の例では、ポリシー **dailyafs** を使用して、ストレージ アカウント **testStorageAcct** の下にある Azure ファイル共有 **testAzureFileShare** の保護を有効にします。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

このコマンドは、保護の構成ジョブが完了し、下に示すような出力が得られるまで待機します。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

**Backup-AzRecoveryServicesBackupItem** を使用して、保護されている Azure ファイル共有のバックアップ ジョブをトリガーします。 次のコマンドを使用して、その中のストレージ アカウントとファイル共有を取得し、オンデマンド バックアップをトリガーします。

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

このコマンドは、次の例に示すように、追跡する必要がある ID を持つジョブを返します。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

バックアップ作成時には Azure ファイル共有のスナップショットが使用されるため、通常は、コマンドからこの出力が返されるまでにジョブが完了します。

### <a name="modify-the-protection-policy"></a>保護ポリシーを変更する

Azure ファイル共有の保護に使用するポリシーを変更するには、関連するバックアップ項目と新しい保護ポリシーを指定して、**Enable-AzRecoveryServicesBackupProtection** を使用します。

次の例では、**testAzureFS** の保護ポリシーを **dailyafs** から **monthlyafs** に変更します。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Azure ファイル共有と Azure ファイルを復元する

ファイル共有全体を、元の場所または代わりの場所に復元できます。 同様に、ファイル共有の個々のファイルを復元することもできます。

### <a name="fetch-recovery-points"></a>復旧ポイントをフェッチする

**Get-AzRecoveryServicesBackupRecoveryPoint** コマンドレットを使用して、バックアップ項目のすべての復旧ポイントを一覧表示します。 次のスクリプトでは、**$rp** 変数が、過去 7 日間に選択したバックアップ項目の復旧ポイントの配列になっています。 この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは **0** です。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 次の例では、**$rp[0]** によって最新の復旧ポイントが選択されます。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

出力は次の例のようになります。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

関連する復旧ポイントが選択されたら、下で説明するように、代わりの場所または元の場所にファイル共有またはファイルを復元します。

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Azure ファイル共有を代わりの場所に復元する

#### <a name="restore-an-azure-file-share"></a>Azure ファイル共有を復元する

以下の情報を指定して、代わりの場所を識別します。

* **TargetStorageAccountName**:バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **TargetFileShareName**:バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **TargetFolder**:データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
* **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

restore コマンドにこれらのパラメーターを指定して、バックアップされたファイル共有を代わりの場所に復元します。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

このコマンドでは、次の例に示すように、追跡する必要がある ID を持つジョブが返されます。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

#### <a name="restore-an-azure-file"></a>Azure ファイルを復元する

ファイル共有全体ではなく個々のファイルを復元するには、以下のパラメーターを指定して、個々のファイルを一意に識別する必要があります。

* **TargetStorageAccountName**:バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **TargetFileShareName**:バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **TargetFolder**:データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
* **SourceFilePath**:文字列で表わされた、ファイル共有内の復元するファイルの絶対パス。 このパスは **Get-AzStorageFile** PowerShell コマンドレットで使用されるのと同じパスです。
* **SourceFileType**:選択されるのはディレクトリかファイルか。 **Directory** または **File** を指定できます。
* **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

追加のパラメーターは、復元する個々のファイルにのみ関連しています。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

このコマンドでは、前述のように、追跡する必要がある ID を持つジョブも返されます。

### <a name="restore-azure-file-shares-to-the-original-location"></a>Azure ファイル共有を元の場所に復元する

元の場所に復元する場合、復元先とターゲットに関連するすべてのパラメーターを指定する必要はありません。 **ResolveConflict** のみ指定する必要があります。

#### <a name="overwrite-an-azure-file-share"></a>Azure ファイル共有を上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure ファイルを上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>バックアップ ジョブと復元ジョブを追跡する

オンデマンドのバックアップ操作や復元操作では、前のセクション「[オンデマンド バックアップをトリガーする](#trigger-an-on-demand-backup)」で示したように、ID と共にジョブが返されます。 **Get-AzRecoveryServicesBackupJobDetails** コマンドレットを使用して、ジョブの進行状況を追跡し、詳細をフェッチします。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
