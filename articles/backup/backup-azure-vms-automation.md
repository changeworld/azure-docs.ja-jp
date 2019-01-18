---
title: Resource Manager でデプロイされた VM のバックアップを PowerShell を使用してデプロイおよび管理する
description: PowerShell を使用して Azure で Resource Manager によりデプロイされた VM のバックアップをデプロイおよび管理する
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2cdeea546e7153c63cb1edfbc53f3644facc4f2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743903"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>PowerShell を使用して仮想マシンをバックアップし、復元する

この記事では、Azure PowerShell コマンドレットを使用して Recovery Services コンテナーに Azure 仮想マシン (VM) をバックアップする方法と Recovery Services コンテナーから Azure 仮想マシンを回復する方法について説明します。 Recovery Services コンテナーは、Azure Backup サービスと Azure Site Recovery サービスでデータと資産を保護するために使用される Azure Resource Manager のリソースです。

> [!NOTE]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager モデルで作成された VM を対象とします。
>
>

PowerShell を使用した VM の保護および復旧ポイントからデータの復元について説明します。

## <a name="concepts"></a>概念

Azure Backup サービスに関する知識が十分でない場合は、「[Azure Backup とは](backup-introduction-to-azure-backup.md)」という記事を参照してください。 開始する前に、Azure Backup に必要な前提条件が満たされていることを確認し、現在の VM バックアップ ソリューションの制限事項を把握してください。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRm.RecoveryServices.Backup PowerShell コマンドレット リファレンスを確認するには、Azure ライブラリの「[Azure Backup - Recovery Services Cmdlets (Azure Backup - Recovery Services コマンドレット)](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)」を参照してください。

## <a name="setup-and-registration"></a>セットアップと登録

開始するには

1. [最新バージョンの PowerShell をダウンロードします](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (必要な最小バージョンは1.4.0 です)

2. 以下のコマンドを入力して、使用可能な Azure Backup の PowerShell コマンドレットを検索します。

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Azure Backup、Azure Site Recovery、Recovery Services コンテナーのエイリアスとコマンドレットが表示されます。 次の画像は表示例です。 コマンドレットの完全な一覧ではありません。

    ![Recovery Services の一覧](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. **Connect-AzureRmAccount** を使用して Azure アカウントにサインインします。 このコマンドレットを実行すると、アカウントの資格情報を入力する Web ページが表示されます。

    * または、**-Credential** パラメーターを使用して、**Connect-AzureRmAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含められます。
    * CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当するテナント ID またはテナントのプライマリ ドメイン名で指定してください。 例: **Connect-AzureRmAccount -Tenant "fabrikam.com"**

4. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup を初めて使用する場合、**[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 次のコマンドを使用して、プロバイダーが正しく登録されたことを確認できます。
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
    コマンドの出力では、**RegistrationState** が **Registered** に変更されるはずです。 そうでない場合は、**[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** コマンドレットをもう一度実行してください。

次のタスクは PowerShell を使用して自動化することができます。

* [Recovery Services コンテナーの作成](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Azure VM のバックアップ](backup-azure-vms-automation.md#back-up-azure-vms)
* [バックアップ ジョブのトリガー](backup-azure-vms-automation.md#trigger-a-backup-job)
* [バックアップ ジョブの監視](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Azure VM の復元](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Recovery Services コンテナーは Resource Manager リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** コマンドレットを使って新しいリソース グループを作成することもできます。 新しいリソース グループを作成するときは、リソース グループの名前と場所を指定します。  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** コマンドレットを使用して Recovery Services コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 使用するストレージ冗長性の種類を指定します。[ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy-lrs.md) または [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy-grs.md) を使用できます。 次に示す例では、testvault の -BackupStorageRedundancy オプションが GeoRedundant に設定されています。

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、**[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** を使用します。

```powershell
Get-AzureRmRecoveryServicesVault
```

出力は次のサンプルのようなものになります。関連する ResourceGroupName と Location が指定されていることに注目してください。

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Azure VM のバックアップ

Recovery Services コンテナーを使用して仮想マシンを保護します。 保護を適用する前に、資格情報コンテナーのコンテキスト (資格情報コンテナーで保護されているデータの種類) を設定し、保護ポリシーを確認します。 保護ポリシーは、バックアップ ジョブが実行される時間と各バックアップ スナップショットの保持期間を設定するスケジュールです。

### <a name="set-vault-context"></a>コンテナーのコンテキストの設定

VM 上の保護を有効にする前に、**[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** を使用してコンテナーのコンテキストを設定します。 コンテナーのコンテキストを設定すると、後続のすべてのコマンドレットに適用されます。 次の例は、コンテナー *testvault* のコンテナーのコンテキストを設定します。

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>保護ポリシーの作成

Recovery Services コンテナーの作成時に、既定の保護およびアイテム保持ポリシーも付属しています。 既定の保護ポリシーは、毎日指定した時刻にバックアップ ジョブをトリガーします。 既定のアイテム保持ポリシーは、毎日の復旧ポイントを 30 日間保持します。 既定のポリシーを使用すると、VM を迅速に保護することができ、後で異なる詳細な内容にポリシーを編集することもできます。

**[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** を使用し、コンテナーで利用できる保護ポリシーを表示します。 このコマンドレットを使用して、特定のポリシーを取得したり、ワークロードの種類に関連付けられているポリシーを表示したりできます。 次の例では、ワークロードの種類 AzureVM のポリシーを取得します。

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

出力は次の例のようになります。

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell の BackupTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルにバックアップ時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。
>
>

バックアップ保護ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられます。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間を定義します。 既定のアイテム保持ポリシーを表示するには、**[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** を使用します。  同様に **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** を使用して、既定のスケジュール ポリシーを取得できます。 **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。 スケジュール ポリシーとアイテム保持ポリシー オブジェクトは、**[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** コマンドレットの入力として使用されます。 次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 次の例では、これらの変数を使用して、保護ポリシー *NewPolicy* の作成時にパラメーターを定義します。

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

出力は次の例のようになります。

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>保護を有効にする

保護ポリシーを定義したら、アイテムのポリシーも有効にする必要があります。 **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** を使用して、保護を有効にします。 保護を有効にするには、アイテムとポリシーの 2 つのオブジェクトが必要です。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールで定義された時刻にバックアップのワークフローが開始されます。

以下の例では、ポリシー NewPolicy を使用してアイテム V2VM の保護を有効にします。 例は、VM が暗号化されるかどうか、暗号化の種類によって異なります。 

**暗号化されていない Resource Manager VM** で保護を有効にするには:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

**暗号化された VM (BEK と KEK を使用して暗号化)** で保護を有効にするには、キーとシークレットをキー コンテナーから読み取るためのアクセス許可を Azure Backup サービスに付与する必要があります。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

**暗号化された VM (BEK だけを使用して暗号化)** で保護を有効にするには、シークレットをキー コンテナーから読み取るためのアクセス許可を Azure Backup サービスに付与する必要があります。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Azure Government クラウドを使用している場合は、[Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) コマンドレットの **-ServicePrincipalName** パラメーターで、値 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 を使用します。
>
>

クラシック VM で保護を有効にするには:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>保護ポリシーの変更

保護ポリシーを変更するには、[Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) を使用して SchedulePolicy または RetentionPolicy オブジェクトを変更します。

次の例では、復旧ポイントのリテンション期間を 365 日に変更します。

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>バックアップをトリガーする

バックアップ ジョブをトリガーするには、**[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** を使用します。 初回バックアップの場合、完全バックアップが行われます。 以後のバックアップは、増分コピーとなります。 バックアップ ジョブをトリガーする前に、**[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** を使用してコンテナーのコンテキストを設定してください。 次の例では、コンテナーのコンテキストが既に設定されていると仮定しています。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

出力は次の例のようになります。

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell での StartTime フィールドと EndTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルに時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。
>
>

## <a name="monitoring-a-backup-job"></a>バックアップ ジョブの監視

Azure ポータルを使用せず、バックアップ ジョブなどの実行時間の長い操作を監視できます。 進行中のジョブの状態を取得するには、**[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** コマンドレットを使用します。 このコマンドレットは、特定のコンテナーのバックアップ ジョブを取得し、そのコンテナーはコンテナーのコンテキストで指定されます。 次の例では、配列として進行中のジョブの状態を取得し、状態を $joblist 変数に格納します。

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

出力は次の例のようになります。

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

完了確認のためにこれらのジョブをポーリングすると、不要な追加コードが発生します。そのため、代わりに **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** コマンドレットを使用します。 このコマンドレットは、ジョブが完了するまで、または指定したタイムアウト値に到達するまで、実行を一時停止します。

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Azure VM の復元

VM の復元に Azure ポータルを使用した場合と PowerShell を使用した場合の間には重要な違いがあります。 PowerShell の場合、復旧ポイントからディスクと構成情報が作成されたら、復元操作は完了します。 復元操作で仮想マシンは作成されません。 ディスクから仮想マシンを作成するには、「[復元されたディスクからの VM の作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)」を参照してください。 VM 全体を復元しないが、Azure VM バックアップからいくつかのファイルを復元または復旧する場合は、[ファイルの回復に関するセクション](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)を参照してください。

> [!Tip]
> 復元操作で仮想マシンは作成されません。
>
>

次の図には、RecoveryServicesVault から BackupRecoveryPoint までのオブジェクト階層が示されています。

![Recovery Services object hierarchy showing BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

バックアップ データを復元するには、バックアップ項目と、復元する特定の時点のデータを保持する復旧ポイントを特定します。 **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** を使用し、コンテナーからアカウントにデータを復元します。

Azure VM を復元する基本的な手順は次のとおりです。

* VM を選択します。
* 復元ポイントを選択します。
* ディスクを復元します。
* 保存されたディスクから VM を作成します。

### <a name="select-the-vm"></a>VM の選択

バックアップする正しいアイテムを特定する PowerShell オブジェクトを取得するには、資格情報コンテナー内のコンテナーから開始し、オブジェクト階層の上から下に進みます。 VM を表すコンテナーを選択するには、**[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** コマンドレットを使用し、**[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** コマンドレットへのパイプとして使用します。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>復元ポイントの選択

**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** コマンドレットを使用して、バックアップ項目のすべての復旧ポイントを一覧表示します。 その後、復元に使用する復旧ポイントを選択します。 使用する復旧ポイントがわからない場合、一覧にある最新の RecoveryPointType = AppConsistent ポイントを選択することをお勧めします。

次のスクリプトでは、**$rp** 変数が、過去 7 日間に選択したバックアップ項目の復旧ポイントの配列になっています。 この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは 0 です。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 次の例では、$rp[0] は最新の復旧ポイントを選択します。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

出力は次の例のようになります。

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>ディスクの復元

**[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** コマンドレットを使用して、復旧ポイントまでバックアップ項目のデータと構成を復元します。 復旧ポイントを特定したら、それを **-RecoveryPoint** パラメーターの値として使用します。 上のサンプルでは、使用する復旧ポイントとして **$rp[0]** が選択されていました。 次のサンプル コードでは、 **$rp[0]** をディスクの復元に使用する復旧ポイントとして指定しています。

ディスクと構成情報を復元するには、次のコマンドを実行します。

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>マネージド ディスクの復元

> [!NOTE]
> バックアップされた VM でマネージド ディスクが使用されている場合、それらをマネージド ディスクとして復元できるよう、Azure Powershell バージョン 6.7.0 以降には新機能が導入されています。
>
>

追加のパラメーター **TargetResourceGroupName** で、マネージド ディスクの復元先とする RG を指定できます。

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** ファイルはストレージ アカウントに復元され、マネージド ディスクは指定したターゲット RG に復元されます。

出力は次の例のようになります。

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

**[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** コマンドレットを使用して、復元ジョブが完了するまで待機します。

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

復元ジョブが完了したら、**[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** コマンドレットを使用して復元操作の詳細を取得します。 JobDetails プロパティには、VM を再構築するために必要な情報が含まれています。

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

ディスクを復元したら、次のセクションに移動して VM を作成します。

## <a name="create-a-vm-from-restored-disks"></a>復元されたディスクからの VM の作成

ディスクを復元したら、次の手順でディスクから仮想マシンを作成し、構成します。

> [!NOTE]
> 復元されたディスクから暗号化された VM を作成するには、Azure のロールに **Microsoft.KeyVault/vaults/deploy/action** の実行が許可されている必要があります。 ロールにこのアクセス許可がない場合は、この操作でカスタム ロールを作成します。 詳細については、「[Azure RBAC のカスタム ロール](../role-based-access-control/custom-roles.md)」をご覧ください。
>
>

> [!NOTE]
> ディスクを復元した後、新しい VM の作成に直接使用できるデプロイ テンプレートを取得できます。 暗号化された/暗号化されていないマネージド/アンマネージド VM を作成するための別の PS コマンドレットはこれ以上なくなりました。

結果のジョブの詳細には、クエリを実行してデプロイできるテンプレート URI が示されます。

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

[ここ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source)で説明しているように、テンプレートをデプロイするだけで新しい VM が作成されます。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

次のセクションでは、"VMConfig" ファイルを使用して VM を作成するために必要な手順を示します。

> [!NOTE]
> VM を作成するには、上記で詳しく説明したデプロイ テンプレートを使用することを強くお勧めします。 このセクション (ポイント 1 ～ 6) は、まもなく非推奨となる予定です。

1. 復元されたディスクのプロパティに対し、ジョブの詳細を照会します。

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Azure Storage コンテキストを設定し、JSON 構成ファイルを復元します。

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. JSON 構成ファイルを使用して VM 構成を作成します。

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. OS ディスクとデータ ディスクを接続します。 この手順では、さまざまな管理対象/暗号化 VM 構成の例を提示します。 自分の VM 構成に最適なサンプルを利用してください。

   * **管理対象外の暗号化されていない VM** - 管理対象外の暗号化されていない VM には、次のサンプルを使用します。

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **管理対象外の暗号化された VM (BEK のみ)** - 管理対象外の暗号化された VM (BEK のみを使用して暗号化) では、ディスクをアタッチする前に、キー コンテナーにシークレットを復元する必要があります。 詳細については、記事「[Azure Backup 復旧ポイントから暗号化された仮想マシンを復元する](backup-azure-restore-key-secret.md)」を参照してください。 次の例では、暗号化された VM の OS とデータ ディスクをアタッチする方法を示します。 OS ディスクの設定時には、関連する OS の種類が示されていることを確認してください。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```
      データ ディスクの暗号化を手動で有効にするには、次のコマンドを使用します。

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
      ```

   * **管理対象外の暗号化された VM (BEK と KEK)** - 管理対象外の暗号化された VM (BEK と KEK を使用して暗号化) では、ディスクをアタッチする前に、キー コンテナーにキーとシークレットを復元します。 詳細については、記事「[Azure Backup 復旧ポイントから暗号化された仮想マシンを復元する](backup-azure-restore-key-secret.md)」を参照してください。 次の例では、暗号化された VM の OS とデータ ディスクをアタッチする方法を示します。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
      ```

      データ ディスクの暗号化を手動で有効にするには、次のコマンドを使用します。

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **管理対象の暗号化されていない VM** - 管理対象の暗号化されていない VM では、復元されたマネージド ディスクをアタッチします。 詳細については、記事「[PowerShell を使用して Windows VM にデータ ディスクを接続する](../virtual-machines/windows/attach-disk-ps.md)」を参照してください。

   * **管理対象の暗号化された VM (BEK のみ)** - 管理対象の暗号化された VM (BEK のみを使用して暗号化) では、復元されたマネージド ディスクをアタッチします。 詳細については、記事「[PowerShell を使用して Windows VM にデータ ディスクを接続する](../virtual-machines/windows/attach-disk-ps.md)」を参照してください。

     データ ディスクの暗号化を手動で有効にするには、次のコマンドを使用します。

       ```powershell
       Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
       ```

   * **管理対象の暗号化された VM (BEK と KEK)** - 管理対象の暗号化された VM (BEK と KEK を使用して暗号化) では、復元されたマネージド ディスクをアタッチします。 詳細については、記事「[PowerShell を使用して Windows VM にデータ ディスクを接続する](../virtual-machines/windows/attach-disk-ps.md)」を参照してください。

      データ ディスクの暗号化を手動で有効にするには、次のコマンドを使用します。

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

5. ネットワーク設定を設定します。

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 仮想マシンを作成します。

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM バックアップからのファイルの復元

Azure VM バックアップからは、ディスクを復元するだけでなく、個々のファイルを復元することもできます。 ファイルの復元機能では、復旧ポイントのすべてのファイルにアクセスできます。 通常のファイルの場合と同様に、ファイル エクスプローラーを使用してファイルを管理します。

Azure VM バックアップからファイルを復元する基本的な手順は次のとおりです。

* VM の選択
* 復元ポイントの選択
* 復旧ポイントのディスクのマウント
* 必要なファイルのコピー
* ディスクのマウント解除

### <a name="select-the-vm"></a>VM の選択

バックアップする正しいアイテムを特定する PowerShell オブジェクトを取得するには、資格情報コンテナー内のコンテナーから開始し、オブジェクト階層の上から下に進みます。 VM を表すコンテナーを選択するには、**[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** コマンドレットを使用し、**[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** コマンドレットへのパイプとして使用します。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>復元ポイントの選択

**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** コマンドレットを使用して、バックアップ項目のすべての復旧ポイントを一覧表示します。 その後、復元に使用する復旧ポイントを選択します。 使用する復旧ポイントがわからない場合、一覧にある最新の RecoveryPointType = AppConsistent ポイントを選択することをお勧めします。

次のスクリプトでは、**$rp** 変数が、過去 7 日間に選択したバックアップ項目の復旧ポイントの配列になっています。 この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは 0 です。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 次の例では、$rp[0] は最新の復旧ポイントを選択します。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

出力は次の例のようになります。

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>復旧ポイントのディスクのマウント

**[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** コマンドレットを使用して、スクリプトで復旧ポイントのすべてのディスクをマウントします。

> [!NOTE]
> ディスクは、iSCSI に接続されたディスクとして、スクリプトが実行されているマシンにマウントされます。 マウントはすぐに行われます。課金は発生しません。
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

出力は次の例のようになります。

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

ファイルの回復先となるマシンでこのスクリプトを実行します。 スクリプトを実行するには、与えられたパスワードを入力する必要があります。 ディスクが接続されたら、エクスプローラーを使用し、新しいボリュームとファイルを参照します。 詳細については、「[Azure 仮想マシンのバックアップからファイルを回復する](backup-azure-restore-files-from-vm.md)」というバックアップに関する記事を参照してください。

### <a name="unmount-the-disks"></a>ディスクのマウント解除

必要なファイルがコピーされたら、**[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** を使用してディスクのマウントを解除します。 復旧ポイントのファイルへのアクセスが削除されるように、必ずディスクのマウントを解除してください。

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>次の手順

PowerShell で Azure リソースを使用する場合、[Windows Server へのバックアップのデプロイおよび管理](backup-client-automation.md)に関する PowerShell 記事を参照してください。 DPM バックアップを管理する場合は、記事「[DPM 用の Backup のデプロイと管理](backup-dpm-automation.md)」を参照してください。 両方の記事で、Resource Manager デプロイとクラシック デプロイの両方のモデルについて説明しています。  
