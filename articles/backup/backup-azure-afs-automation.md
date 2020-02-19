---
title: PowerShell を使用して Azure Files をバックアップする
description: この記事では、Azure Backup サービスと PowerShell を使用して Azure Files をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120509"
---
# <a name="back-up-azure-files-with-powershell"></a>PowerShell を使用して Azure Files をバックアップする

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを使用して Azure Files のファイル共有をバックアップするために Azure PowerShell を使用する方法を説明します。

この記事では、以下の方法について説明します。

> [!div class="checklist"]
>
> * PowerShell を設定し、Azure Recovery Services プロバイダーを登録します。
> * Recovery Services コンテナーを作成する。
> * Azure ファイル共有のバックアップを構成する。
> * バックアップ ジョブを実行します。

## <a name="before-you-start"></a>開始する前に

* Recovery Services コンテナーについての[詳細情報](backup-azure-recovery-services-vault-overview.md)を確認します。
* [Azure ファイル共有のバックアップ](backup-afs.md)に関するプレビュー機能を確認します。
* Recovery Services の PowerShell オブジェクト階層を確認します。

## <a name="recovery-services-object-hierarchy"></a>Recovery Services オブジェクトの階層

オブジェクト階層の概要を次の図に示します。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure ライブラリの **Az.RecoveryServices** [コマンドレット リファレンス](/powershell/module/az.recoveryservices)のリファレンスを確認します。

## <a name="set-up-and-install"></a>設定とインストール

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を次のように設定します。

1. [最新バージョンの Az PowerShell をダウンロードします](/powershell/azure/install-az-ps)。 必要な最小バージョンは 1.0.0 です。

> [!WARNING]
> プレビューに必要な PS の最小バージョンは "Az 1.0.0" でした。 GA に向けた今後の変更により、必要とされる PS の最小バージョンは "Az.RecoveryServices 2.6.0" になります。 すべての既存の PS バージョンをこのバージョンにアップグレードすることが非常に重要です。 そうしないと、既存のスクリプトは GA 後に中断されます。 次の PS コマンドを使用して最小バージョンをインストールします。

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 次のコマンドを使用して、Azure Backup PowerShell コマンドレットを検索します。

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup、Azure Site Recovery、Recovery Services コンテナーの別名とコマンドレットを確認します。 表示例を次に示します。 これはコマンドレットの完全な一覧ではありません。

    ![Recovery Services コマンドレットの一覧](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount** を使用して Azure アカウントにサインインします。
5. 表示される Web ページで、アカウントの資格情報を入力するように求められます。

    * または、 **-Credential** を使用して、**Connect-AzAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含めることもできます。
    * CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当する tenantID またはテナントのプライマリ ドメイン名で指定してください。 たとえば、「**Connect-AzAccount -Tenant** fabrikam.com」を指定します。

6. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. プロバイダーが正常に登録されたことを確認します。

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. コマンド出力で、**RegistrationState** が **Registered** に変わっていることを確認します。 変わっていない場合は、**Register-AzResourceProvider** コマンドレットをもう一度実行します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは Resource Manager のリソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**New-AzResourceGroup** コマンドレットを使ってリソース グループを作成することもできます。 リソース グループを作成するときは、リソース グループの名前と場所を指定します。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. コンテナーはリソース グループに配置されます。 既存のリソース グループがない場合は、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) を使用して新しいリソース グループを作成します。 この例では、新しいリソース グループを米国西部リージョンに作成します。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) コマンドレットを使用して、コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. コンテナー ストレージに使用する冗長性の種類を指定します。

   * [ローカル冗長ストレージ](../storage/common/storage-redundancy-lrs.md)または [geo 冗長ストレージ](../storage/common/storage-redundancy-grs.md)を使用できます。
   * 次の例では、**testvault** の [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) コマンドの **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、[Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0) を使用します。

```powershell
Get-AzRecoveryServicesVault
```

次のように出力されます。 関連するリソース グループと場所が指定されていることに注目してください。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する

このコンテナー オブジェクトを変数に格納し、そのコンテナーのコンテキストを設定します。

* Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要とするので、コンテナー オブジェクトを変数に格納すると便利です。
* コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。 これを、[Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) を使用して設定します。 コンテキストが設定されると、それが後続のすべてのコマンドレットに適用されます。

次の例では、**testvault** のコンテナーのコンテキストを設定します。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>コンテナー ID をフェッチする

コンテナーのコンテキストの設定は、Azure PowerShell のガイドラインに従って廃止される予定です。 代わりに、次のようにコンテナー ID を格納またはフェッチし、関連するコマンドに渡すことができます。 そのため、コンテナーのコンテキストを設定していない場合や、特定のコンテナーに対して実行するコマンドを指定する場合は、次のように関連するすべてのコマンドにコンテナー ID を "-vaultID" として渡します。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

バックアップ ポリシーは、バックアップのスケジュール、およびバックアップの復旧ポイントを保持する期間を指定します。

* バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間が定義されています。
* [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー リテンション期間を表示します。
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー スケジュールを表示します。
* 新しいバックアップ ポリシーを作成するには、[New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) コマンドレットを使用します。 スケジュール ポリシー オブジェクトとアイテム保持ポリシー オブジェクトを入力します。

既定では、開始時刻はスケジュール ポリシー オブジェクトで定義されます。 開始時刻を希望の時刻に変更するには、次の例を使用します。 希望の開始時刻も、UTC で指定する必要があります。 次の例では、毎日のバックアップの希望の開始時刻が午前 01:00 UTC であるものとします。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 開始時刻は 30 分単位のみで指定する必要があります。 上の例では、"01:00:00" または "02:30:00" のみを指定できます。 開始時刻を "01:15:00" にすることはできません。

次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 その後に、それらの変数を新しいポリシー (**NewAFSPolicy**) のパラメーターとして使用しています。 **NewAFSPolicy** は毎日のバックアップを受け取り、30 日間保持します。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

次のように出力されます。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>バックアップの有効化

バックアップ ポリシーを定義したら、このポリシーを使用して、Azure ファイル共有の保護を有効にできます。

### <a name="retrieve-a-backup-policy"></a>バックアップ ポリシーを取得する

[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) を使用して、関連するポリシー オブジェクトをフェッチします。 このコマンドレットを使用して、特定のポリシーを取得したり、ワークロードの種類に関連付けられているポリシーを表示したりできます。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>ワークロードの種類のポリシーを取得する

次の例では、ワークロードの種類が **AzureFiles** であるポリシーを取得します。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

次のように出力されます。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell の **BackupTime** フィールドのタイム ゾーンは協定世界時 (UTC) です。 Azure portal 上でバックアップ時刻が表示されるときに、時刻はローカル タイム ゾーンに調整されます。

### <a name="retrieve-a-specific-policy"></a>特定のポリシーを取得する

次のポリシーでは、**dailyafs** という名前のバックアップ ポリシーを取得します。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>バックアップを有効にしてポリシーを適用する

[Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) を使用して、保護を有効にします。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールに従ってバックアップがトリガーされます。

次の例では、ポリシー **dailyafs** を使用して、ストレージ アカウント **testStorageAcct** の Azure ファイル共有 **testAzureFileShare** の保護を有効にしています。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

このコマンドは、保護の構成ジョブが完了し、下に示すような出力が得られるまで待機します。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>重要な注意 - AFS バックアップのバックアップ項目の識別

このセクションでは、GA に向けての準備における、AFS バックアップの重要な変更点について説明します。

AFS のバックアップを有効にするときに、ユーザーにわかりやすいファイル共有名をエンティティ名としてユーザーが指定し、バックアップ項目が作成されます。 バックアップ項目の "名前" は、Azure Backup サービスによって作成される一意の識別子です。 通常、識別子にはユーザー フレンドリ名が含まれます。 しかし、ファイル共有を削除して同じ名前で別のファイル共有を作成できるという論理的な削除の重要なシナリオを処理するために、Azure ファイル共有の一意の ID をユーザー フレンドリ名ではなく ID とすることになりました。 各項目の一意の ID または名前を確認するには、backupManagementType および WorkloadType に適切なフィルターを指定して ```Get-AzRecoveryServicesBackupItem``` コマンドを実行し、関連するすべての項目を取得してから、返された PS オブジェクトまたは応答の name フィールドを確認します。 常に、項目を一覧表示したうえで、応答の "name" フィールドから一意の名前を取得することをお勧めします。 この値を "Name" パラメーターで使用して、項目をフィルター処理します。 または、FriendlyName パラメーターを使用して、そのユーザー フレンドリ名または識別子を持つ項目を取得します。

> [!WARNING]
> PS バージョンが、AFS バックアップ用の "Az.RecoveryServices 2.6.0" のための最小バージョンにアップグレードされていることを確認してください。 このバージョンでは、```Get-AzRecoveryServicesBackupItem``` コマンドで "friendlyName" フィルターを使用できます。 Azure ファイル共有名を friendlyName パラメーターに渡します。 Azure ファイル共有名を "Name" パラメーターに渡すと、このバージョンでは、このフレンドリ名をフレンドリ名パラメーターに渡すための警告がスローされます。 この最小バージョンをインストールしないと、既存のスクリプトでエラーが発生する可能性があります。 次のコマンドを使用して PS の最小バージョンをインストールします。

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

[Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) を使用して、保護されている Azure ファイル共有のオンデマンド バックアップを実行します。

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) を使用して、バックアップ データを保持するコンテナー内のコンテナーからストレージ アカウントを取得します。
2. バックアップ ジョブを開始するには、[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) を使用して、Azure ファイル共有に関する情報を取得します。
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) を使用して、オンデマンド バックアップを実行します。

次のようにしてオンデマンド バックアップを実行します。

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

このコマンドでは、次の例に示すように、追跡する必要がある ID を持つジョブが返されます。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

バックアップ作成時には Azure ファイル共有のスナップショットが使用されるため、通常は、コマンドからこの出力が返されるまでにジョブが完了します。

### <a name="using-on-demand-backups-to-extend-retention"></a>オンデマンド バックアップを使用して保持期間を延長する

オンデマンド バックアップを使用すると、スナップショットを 10 年間保持することができます。 スケジューラを使い、保持期間を選択して PowerShell スクリプトをオンデマンドで実行することで、スナップショットを定期的 (週次、月次、年次) に作成することも可能です。 定期的なスナップショットの取得については、Azure Backup を使用した[オンデマンド バックアップの制限](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)に関するページを参照してください。

サンプル スクリプトをお探しの場合は、バックアップを定期的にスケジュールし、それを最大 10 年間保持することが可能な Azure Automation Runbook を使用した GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) 上のサンプル スクリプトを参照できます。

> [!WARNING]
> お使いの Automation の Runbook で、PS バージョンが、AFS バックアップ用の "Az.RecoveryServices 2.6.0" のための最小バージョンにアップグレードされていることを確認してください。 以前の "AzureRM" モジュールを "Az" モジュールに置き換える必要があります。 このバージョンでは、```Get-AzRecoveryServicesBackupItem``` コマンドで "friendlyName" フィルターを使用できます。 Azure ファイル共有名を friendlyName パラメーターに渡します。 Azure ファイル共有名を "Name" パラメーターに渡すと、このバージョンでは、このフレンドリ名をフレンドリ名パラメーターに渡すための警告がスローされます。

## <a name="next-steps"></a>次のステップ

Azure portal での Azure Files のバックアップについて[学習します](backup-afs.md)。
