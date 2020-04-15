---
title: クラウド ワークロードの保護に役立つセキュリティ機能
description: Azure Backup のセキュリティ機能を使用してバックアップのセキュリティを強化する方法について説明します。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668736"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Azure Backup を使用したクラウド ワークロードを保護するためのセキュリティ機能

マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃から保護するために、Azure Backup では、削除後もバックアップ データを保護するためのセキュリティ機能が提供されるようになりました。

このような機能の 1 つに、論理的な削除があります。 論理的な削除を使用すると、悪意のあるアクターによって VM のバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。 また、Azure では、お使いのデータをさらにセキュリティで保護するために、保存中のすべてのバックアップ データも [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) を使用して暗号化しています。

Azure 仮想マシン用の論理的な削除の保護は一般提供されています。

>[!NOTE]
>Azure VM での SQL Server の論理的な削除と Azure VM ワークロードでの SAP HANA の論理的な削除が、プレビューで利用できるようになりました。<br>
>プレビュー用にサインアップするには、AskAzureBackupTeam@microsoft.com 宛てにご連絡ください

## <a name="soft-delete"></a>論理的な削除

### <a name="soft-delete-for-vms"></a>VM の論理的な削除

VM の論理的な削除によって、ご利用の VM のバックアップが意図しない削除から保護されます。 バックアップは、削除された後でも、追加で 14 日間は論理的な削除状態で保持されます。

> [!NOTE]
> 論理的な削除では、削除されたバックアップ データのみが保護されます。 バックアップせずに VM が削除された場合、論理的な削除機能ではデータは保持されません。 完全な復元性を確保するには、すべてのリソースを Azure Backup で保護する必要があります。
>

### <a name="supported-regions"></a>サポートされているリージョン

現在、論理的な削除は、米国中西部、東アジア、カナダ中部、カナダ東部、フランス中部、フランス南部、韓国中部、韓国南部、英国南部、英国西部、オーストラリア東部、オーストラリア南東部、北ヨーロッパ、米国西部、米国西部 2、米国中部、東南アジア、米国中北部、米国中南部、東日本、西日本、インド南部、インド中部、インド西部、米国東部 2、スイス北部、スイス西部、ノルウェー西部、ノルウェー東部など、すべての国のリージョンでサポートされています。

### <a name="soft-delete-for-vms-using-azure-portal"></a>Azure portal を使用した VM の論理的な削除

1. VM のバックアップ データを削除するには、バックアップを停止する必要があります。 Azure portal で、Recovery Services コンテナーにアクセスし、バックアップ項目を右クリックして、 **[バックアップの停止]** を選択します。

   ![Azure portal の [バックアップ項目] のスクリーンショット](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 次のウィンドウでは、バックアップ データを削除するか保持するかを選択できます。 **[バックアップデータの削除]** を選択してから **[バックアップの停止]** を選択した場合、VM のバックアップは完全に削除されません。 もっと正確に言えば、バックアップ データは論理的な削除状態で 14 日間保持されます。 **[バックアップ データの削除]** を選択した場合は、削除の電子メール アラートが構成済みのメール ID に送信され、バックアップ データの保有延長が 14 日間残っていることがユーザーに通知されます。 また、12 日目に、削除されたデータを復元できる期間があと 2 日間残っていることを知らせる電子メール アラートが送信されます。 削除は 15 日目まで延期されます。ここで完全な削除が行われ、データが永久に削除されたことを知らせる最終的な電子メール アラートが送信されます。

   ![Azure portal の [バックアップの停止] 画面のスクリーンショット](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. この 14 日間は、Recovery Services コンテナーで、論理的に削除された VM の横に赤い "論理的な削除" アイコンが付けられて表示されます。

   ![論理的な削除状態の VM を示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 論理的に削除されたバックアップ項目がコンテナーに存在する場合、その時点でコンテナーを削除することはできません。 バックアップ項目が完全に削除され、コンテナーに論理的な削除状態の項目がなくなってから、コンテナーの削除を試してください。

4. 論理的に削除された VM を復元するには、最初に削除を取り消す必要があります。 削除を取り消すには、論理的に削除された VM を選択し、 **[削除の取り消し]** オプションを選択します。

   ![VM の削除の取り消しを示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   [削除の取り消し] を選択すると、VM のすべての復元ポイントの削除が取り消され、復元操作の実行が可能になるという警告がウィンドウに表示されます。 VM は、"データを保持して保護を停止" の状態で保持され、バックアップは一時停止され、バックアップ データはバックアップ ポリシーが有効になっていない状態で永久に保持されます。

   ![VM の削除の取り消しを確認する Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   この時点で、選択した復元ポイントから **[VM の復元]** を選択して VM を復元することもできます。  

   ![Azure portal の [VM の復元] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > ユーザーが **[バックアップの再開]** 操作を実行した場合にのみ、ガベージ コレクターが実行され、期限切れの回復ポイントがクリーンアップされます。

5. 削除の取り消し処理が完了すると、状態は "データを保持して保護を停止" に戻り、 **[バックアップの再開]** を選択できるようになります。 **[バックアップの再開]** 操作により、ユーザー定義のバックアップと保持スケジュールで選択されたバックアップ ポリシーに関連付けられているバックアップ項目が、アクティブ状態に戻ります。

   ![Azure portal の [バックアップの再開] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/resume-backup.png)

このフロー チャートは、論理的な削除が有効にされているときのバックアップ項目のさまざまな手順と状態を示しています。

![論理的に削除されたバックアップ項目のライフサイクル](./media/backup-azure-security-feature-cloud/lifecycle.png)

詳細については、後述の「[よく寄せられる質問](backup-azure-security-feature-cloud.md#frequently-asked-questions)」を参照してください。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure PowerShell を使用した VM の論理的な削除

> [!IMPORTANT]
> Azure PS で論理的に削除するために必要な Az.RecoveryServices バージョンの最小は 2.2.0 です。 ```Install-Module -Name Az.RecoveryServices -Force``` を使用し、最新バージョンを取得してください。

Azure portal に関する上記の説明にあるように、Azure PowerShell の使用時も手順は同じです。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell を使用したバックアップ項目の削除

[Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS コマンドレットを使用してバックアップ項目を削除します。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

バックアップ項目の "DeleteState" が "NotDeleted" から "ToBeDeleted" に変更されます。 バックアップ データは 14 日間保持されます。 削除操作を元に戻す場合、削除操作を元に戻します。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell を使用して削除操作を元に戻す

まず、(削除予定など) 論理的な削除状態にある関連バックアップ項目をフェッチします。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

次に、[Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS コマンドレットで削除操作を元に戻します。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

バックアップ項目の "DeleteState" が "NotDeleted" に戻ります。 しかし、保護は引き続き停止状態にあります。 [バックアップを再開](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)して、保護を再び有効にします。

### <a name="soft-delete-for-vms-using-rest-api"></a>REST API を使用した VM の論理的な削除

- [こちら](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)の説明に従って、REST API を使用してバックアップを削除します。
- これらの削除操作を元に戻す場合は、[こちら](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)で説明されている手順を参照してください。

## <a name="disabling-soft-delete"></a>論理的な削除の無効化

論理的な削除は、偶発的または悪意のある削除からバックアップ データを保護するために、新しく作成されたコンテナーでは既定で有効になっています。  この機能を無効にすることは推奨されません。 論理的な削除を無効にすることを検討する必要があるのは、保護された項目を新しいコンテナーに移動することを計画していて、削除と再保護の前に (テスト環境などで) 必要な 14 日間待機できない場合のみです。この機能を無効にできるのは、コンテナーの所有者だけです。 この機能を無効にした場合、保護された項目を今後削除すると、復元する機能はなく、すべてがすぐに削除されます。 この機能を無効にする前に、論理的に削除された状態で存在するバックアップ データは、14 日間論理的に削除された状態のままになります。 これらをすぐに完全に削除する場合、完全に削除されるように、削除を取り消してからもう一度削除する必要があります。

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure portal を使用した論理的な削除を無効にする

論理的な削除を無効にするには、次の手順に従います。

1. Azure portal で、ご利用のコンテナーに移動して、 **[設定] ->  **[プロパティ]**** に移動します。
2. プロパティ ウィンドウで、 **[セキュリティ設定] **[更新]**  -> ** を選択します。  
3. [セキュリティ設定] ウィンドウの **[論理的な削除]** で、 **[無効にする]** を選択します。

![論理的な削除の無効化](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure PowerShell を使用した論理的な削除を無効にする

> [!IMPORTANT]
> Azure PS で論理的に削除するために必要な Az.RecoveryServices バージョンの最小は 2.2.0 です。 ```Install-Module -Name Az.RecoveryServices -Force``` を使用し、最新バージョンを取得してください。

無効にするには、[Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS コマンドレットを使用します。

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API を使用した論理的な削除の無効化

REST API を使用して論理的な削除機能を無効にする方法については、[こちら](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)で説明されている手順を参照してください。

## <a name="permanently-deleting-soft-deleted-backup-items"></a>論理的に削除されたバックアップ項目を完全に削除する

この機能を無効にする前に、論理的に削除された状態のバックアップ データは、論理的に削除された状態のままになります。 これらをすぐに完全に削除する場合、削除を取り消し、もう一度削除して完全に削除します。

### <a name="using-azure-portal"></a>Azure Portal の使用

次の手順に従います。

1. [論理的な削除を無効にする](#disabling-soft-delete)には、この手順に従います。
2. Azure portal で、お使いのコンテナーにアクセスし、**バックアップ項目**にアクセスして論理的に削除された VM を選択します。

   ![論理的に削除された VM の選択](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. **[削除の取り消し]** オプションを選択します。

   ![[削除の取り消し] の選択](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. ウィンドウが表示されます。 **[削除の取り消し]** を選択します。

   ![[削除の取り消し] の選択](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. バックアップ データを完全に削除するには、 **[バックアップ データの削除]** を選択します。

   ![[バックアップ データの削除] の選択](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. バックアップ項目の名前を入力して、復旧ポイントを削除してもよいことを確認します。

   ![バックアップ項目の名前の入力](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 項目のバックアップ データを削除するには、 **[削除]** を選択します。 バックアップ データが削除されたことを示す通知メッセージが表示されます。

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

論理的な削除を無効にする前に項目を削除した場合、項目は論理的に削除されている状態になります。 すぐに削除するには、削除操作を元に戻してからもう一度削除する必要があります。

論理的に削除された状態にある項目を特定します。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

次に、論理的な削除が有効になっていたときに実行された削除操作を元に戻します。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

これで論理的な削除が無効になったため、削除操作後、バックアップ データがすぐに削除されるようになります。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API の使用

論理的な削除を無効にする前に項目を削除した場合、項目は論理的に削除されている状態になります。 すぐに削除するには、削除操作を元に戻してからもう一度削除する必要があります。

1. まず、[こちら](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)で説明されている手順に従って、削除操作を元に戻します。
2. 次に、[こちら](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)で説明されている手順に従って、REST API を使用して論理的な削除機能を無効にします。
3. 次に、[こちら](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)の説明に従って、REST API を使用してバックアップを削除します。

## <a name="encryption"></a>暗号化

クラウドに保存されているバックアップ データは、Azure Storage 暗号化を使用して自動的に暗号化されます。これは、セキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 この保存データは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って暗号化され、FIPS 140-2 に準拠しています。

保存データの暗号化に加えて、転送中のすべてのバックアップ データが HTTPS 経由で転送されます。 これは、Azure バックボーン ネットワークにとどまります。

詳細については、「[保存データ向け Azure ストレージの暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)」をご覧ください。 暗号化に関する質問の回答については、[Azure Backup の FAQ](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) のページを参照してください。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化は、ユーザー側から明示的なアクションを実行しなくても有効になり、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>カスタマー マネージド キーを使用したバックアップ データの暗号化

Azure Virtual Machines のバックアップ時に、ご自身で所有および管理しているキーを使って、データを暗号化できるようになりました。 Azure Backup を使用すると、自分のバックアップの暗号化に、Azure Key Vault に格納されているご自身の RSA キーを使用でききます。 バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、ご自身のキーを使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Recovery Services コンテナーに、Azure Key Vault の暗号化キーへのアクセスが許可されている必要があります。 キーの無効化とアクセスの取り消しは、必要に応じていつでも可能です。 ただし、コンテナーに対する項目の保護を試みるには、事前にキーを使って暗号化を有効にしておく必要があります。

>[!NOTE]
>この機能の利用は、現在、制限されています。 カスタマー マネージド キーを使用して、ご自身のバックアップ データを暗号化することをご希望の方は、[こちらのアンケート](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)にご記入のうえ、メール (AskAzureBackupTeam@microsoft.com) でお送りください。 この機能は、Azure Backup サービスが承認した場合に使用できることに注意してください。

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>カスタマー マネージド キー使用して暗号化されたマネージド ディスク VM のバックアップ

Azure Backup を使用すると、サーバー側の暗号化にキーを使用する Azure VM をバックアップすることもできます。 ディスクの暗号化に使用されるキーは Azure Key Vault に格納され、ご自身で管理します。 カスタマー マネージド キーを使用したサーバー側の暗号化 (SSE) は、Azure Disk Encryption (ADE) とは異なります。ADE は BitLocker (Windows の場合) および DM-Crypt (Linux の場合) を利用してゲスト内暗号化を実行する一方で、SSE はストレージ サービス内のデータを暗号化して、VM の任意の OS またはイメージを使用できるようにするからです。 詳細については、[カスタマー マネージド キーを使用したマネージド ディスクの暗号化](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)に関するセクションをご覧ください。

### <a name="backup-of-vms-encrypted-using-ade"></a>ADE を使用して暗号化された VM のバックアップ

Azure Backup を使用すると、Azure Disk Encryption を使って暗号化された OS またはデータ ディスクを含む Azure 仮想マシンをバックアップすることもできます。 ADE は、Windows VM の場合は BitLocker を、Linux VM の場合は DM-Crypt 機能を使用して、ゲスト内暗号化を実行します。 詳細については、[Azure Backup での暗号化された仮想マシンのバックアップと復元](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)に関するページをご覧ください。

## <a name="private-endpoints"></a>プライベート エンドポイント

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>その他のセキュリティ機能

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup 回復ポイントの保護

Recovery Services コンテナーによって使用されるストレージ アカウントは分離されており、悪意のある目的でユーザーがアクセスすることはできません。 アクセスが許可されるのは、復元などの Azure Backup 管理操作だけです。 これらの管理操作は、ロールベースのアクセス制御 (RBAC) によって制御されます。

詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)に関するページを参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="for-soft-delete"></a>論理な削除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>すべてのコンテナーで論理的な削除機能を有効にする必要はありますか?

いいえ。これはすべての Recovery Services コンテナーに組み込まれており、既定で有効になっています。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>削除操作の完了後、データが論理的に削除された状態で保持される日数を構成できますか?

いいえ。削除操作後、追加の保持期間は 14 日間に固定されています。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>この 14 日間の保持期間に対するコストを支払う必要がありますか?

いいえ。この 14 日間の追加の保持期間は、論理的な削除機能の一部として無料で提供されます。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>データが論理的な削除状態であるときに復元操作を実行できますか?

いいえ。復元するには、論理的に削除されたリソースの削除を取り消す必要があります。 削除の取り消し操作により、リソースが**データを保持して保護を停止の状態**に戻り、任意の時点に復元できるようになります。 ガベージ コレクターは、この状態で一時停止のままになります。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>スナップショットは、コンテナー内の回復ポイントと同じライフサイクルに従いますか?

はい。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>論理的に削除されたリソースに対してスケジュールされたバックアップを再びトリガーするにはどうすればよいですか?

削除の取り消し後に再開操作を実行すると、リソースが再度保護されます。 再開操作により、選択した保持期間でスケジュールされたバックアップをトリガーするため、バックアップ ポリシーが関連付けられます。 また、再開操作が完了するとすぐにガベージ コレクターが実行されます。 有効期限を過ぎた回復ポイントから復元を実行する場合は、再開操作をトリガーする前に実行することをお勧めします。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>コンテナー内に論理的に削除された項目がある場合、コンテナーを削除できますか?

Recovery Services コンテナー内に論理的に削除された状態のバックアップ項目がある場合、そのコンテナーを削除することはできません。 論理的に削除された項目は、削除操作の 14 日後に完全に削除されます。 14 日間待機できない場合は、[論理的な削除を無効にし](#disabling-soft-delete)、論理的に削除された項目の削除を取り消して、もう一度削除して完全に削除します。 保護された項目がなく、論理的に削除された項目がないことが確保されたら、コンテナーを削除することができます。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>削除後の 14 日間の論理的な削除期間より前にデータを削除することはできますか?

いいえ。 論理的に削除された項目を強制的に削除することはできません。これらは、14 日後に自動的に削除されます。 このセキュリティ機能は、偶発的または悪意のある削除からバックアップされたデータを保護するために有効になっています。  VM で他の操作を実行する前に、14 日間待機する必要があります。  論理的に削除された項目については課金されません。  新しいコンテナーで 14 日以内の論理的な削除のマークが付けられた VM を再保護する必要がある場合は、Microsoft サポートにお問い合わせください。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell または CLI で論理的な削除操作を実行できますか?

論理的な削除操作は [PowerShell](#soft-delete-for-vms-using-azure-powershell) で実行できます。 現在のところ、CLI はサポートされていません。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>他のクラウド ワークロード (Azure VM での SQL Server や Azure VM での SAP HANA など) では、論理的な削除はサポートされていますか?

いいえ。 現在、論理的な削除は Azure 仮想マシンでのみサポートされています。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のセキュリティ制御](backup-security-controls.md)を確認する。
