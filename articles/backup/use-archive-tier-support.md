---
title: Archive レベルを使用する
description: Azure Backup への Archive レベルのサポートの使用について説明します。
ms.topic: conceptual
ms.date: 10/23/2021
ms.custom: devx-track-azurepowershell-azurecli
zone_pivot_groups: backup-client-powershelltier-clitier-portaltier
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9b35cd9269ef75f7c97d576c85004a4fe0be6a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089845"
---
# <a name="use-archive-tier-support"></a>Archive レベルのサポートを使用する


::: zone pivot="client-powershelltier"

この記事では、PowerShell を使用して、Archive レベル、スナップショット、Standard レベルに長期保有ポイントをバックアップする手順について説明します。

## <a name="supported-workloads"></a>サポートされるワークロード

| ワークロード | オペレーション |
| --- | --- |
| Azure Virtual Machines (プレビュー)   <br><br>  Azure の仮想マシンにおける SQL Server   | <ul><li>アーカイブ可能な復旧ポイントを表示する    </li><li>推奨される復旧ポイントを表示する (Virtual Machines の場合のみ)  </li><li>アーカイブ可能な復旧ポイントを移動する   </li><li>推奨される復旧ポイントを移動する (Azure Virtual Machines の場合のみ)   </li><li>アーカイブされた復旧ポイントを表示する   </li><li>アーカイブされた復旧ポイントから復元する   </li></ul> |

## <a name="get-started"></a>はじめに

1. [最新](https://github.com/PowerShell/PowerShell/releases)バージョンの PowerShell を GitHub からダウンロードします。

1. PowerShell で次のコマンドレットを実行します。
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure に接続します。

1. サブスクリプションにサインインします。

   ```azurepowershell
   Set-AzContext -Subscription "SubscriptionName"
   ```

1. コンテナーの取得:

    ```azurepowershell
    $vault = Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"
    ```

1. バックアップ項目の一覧の取得:

   - **Azure Virtual Machines の場合**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"
       ```

   - **Azure Virtual Machines 内の SQL Server の場合**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"
       ```

1. バックアップ項目を取得します。

   - **Azure Virtual Machines の場合**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}
     ```

   - **Azure Virtual Machines 内の SQL Server の場合**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}
     ```

1. (省略可能) 復旧ポイントを表示する日付範囲を追加します。 たとえば、過去 120 日間の復旧ポイントを表示する場合は、次のコマンドレットを使用します。

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-120)
    $endDate = (Get-Date).AddDays(0) 
   ```
   >[!NOTE]
   >異なる時間範囲の復旧ポイントを表示するには、必要に応じて開始日と終了日を変更します。 <br><br> 既定では、過去 30 日間が取得されます。

## <a name="check-the-archivable-status-of-all-recovery-points"></a>すべての復旧ポイントのアーカイブ可能状態を確認する

次のコマンドレットを使用して、バックアップ項目のすべての復旧ポイントのアーカイブ可能状態を確認できます。

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

## <a name="check-archivable-recovery-points"></a>アーカイブ可能な復旧ポイントを確認する

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

これにより、アーカイブに移動する準備ができている特定のバックアップ項目に関連するすべての復旧ポイントが一覧表示されます (開始日から終了日まで)。 また、開始日と終了日を変更することもできます。

## <a name="check-why-a-recovery-point-cant-be-moved-to-archive"></a>復旧ポイントをアーカイブに移動できない理由を確認する

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

ここで `$rp[0]` は、アーカイブ可能でない理由を確認する復旧ポイントです。

**サンプル出力**

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>アーカイブ可能ポイントの推奨セットを確認する (Azure VM の場合のみ)

仮想マシンに関連付けられている復旧ポイントは増分です。 特定の復旧ポイントをアーカイブに移動すると、完全バックアップに変換されてからアーカイブに移動されます。 そのため、アーカイブへの移動に伴うコスト節約は、データ ソースのチャーンに応じて異なります。

したがって、Azure Backup には、まとめて移動した場合にコストを節約できる復旧ポイントの推奨セットが用意されています。

>[!NOTE]
>- コストの節約額はさまざまな原因によって左右されるため、すべてのインスタンスで同じではない場合があります。
>- 推奨セットに含まれるすべての復旧ポイントを Vault-Archive レベルに移動した場合にのみ、コストを削減できます。

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

## <a name="move-to-archive"></a>アーカイブに移動する

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

ここで、`$rp[0]` は一覧の最初の復旧ポイントです。 他の復旧ポイントを移動する場合は、`$rp[1]`、`$rp[2]` などを使用します。

このコマンドレットを実行すると、アーカイブ可能な復旧ポイントがアーカイブに移動されます。 これによって返されるジョブを使用して、ポータルと PowerShell の両方から移動操作を追跡することができます。

## <a name="view-archived-recovery-points"></a>アーカイブされた復旧ポイントを表示する

このコマンドレットからは、アーカイブされたすべての復旧ポイントが返されます。

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-with-powershell"></a>PowerShell で復元する

アーカイブ内の復旧ポイントについては、Azure Backup には統合型の復元方法が用意されています。
統合型の復元は、2 つのステップから成るプロセスです。

1. アーカイブに格納されている復旧ポイントをリハイドレートします。
2. 一時的に、10 から 30 日の期間 (リハイドレート期間とも呼ばれます)、Vault-Standard レベルに格納します。 既定は 15 日間です。 リハイドレートの優先度には、Standard と High の 2 種類があります。 [リハイドレートの優先度](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority)に関する詳細をご覧ください。

>[!NOTE]
>
>- リハイドレート期間は、いったん選択したら変更できません。また、リハイドレートした復旧ポイントは、リハイドレート期間中は Standard レベルにとどまります。
>- リハイドレートの追加ステップにはコストが発生します。

Azure 仮想マシン用のさまざまな復元方法について詳しくは、[PowerShell を使用した Azure VVM の復元](backup-azure-vms-automation.md#restore-an-azure-vm)に関するページをご覧ください。

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server を復元するには、[これらの手順](backup-azure-sql-automation.md#restore-sql-dbs)に従ってください。 `Restore-AzRecoveryServicesBackupItem` コマンドレットには、`RehydrationDuration` と `RehydrationPriority` の 2 つの追加パラメーターが必要です。

## <a name="view-jobs"></a>ジョブの表示

移動と復元の各ジョブを表示するには、次の PowerShell コマンドレットを使用します。

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="move-recovery-points-to-archive-tier-at-scale"></a>復旧ポイントをアーカイブ層に大規模に移動する

サンプル スクリプトを使用して、大規模に操作を実行できるようになりました。 サンプル スクリプトの実行方法については、[詳細](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md)を確認してください。 スクリプトは[ここ](https://github.com/hiaga/Az.RecoveryServices)からダウンロードできます。

Azure Backup で提供されるサンプル スクリプトを使用して、次の操作を実行できます。

- Azure VM 内の SQL Server の特定のデータベースまたはすべてのデータベースのすべての対象復旧ポイントをアーカイブ層に移動します。
- 特定の Azure 仮想マシンに対して推奨されるすべての復旧ポイントをアーカイブ層に移動します。
 
また、要件に従ってスクリプトを記述し、上記のサンプル スクリプトを変更して必要なバックアップ項目をフェッチすることもできます。

::: zone-end



::: zone pivot="client-clitier"

この記事では、コマンド ライン インターフェイス (CLI) を使用して、Archive レベル、スナップショット、Standard レベルに長期保有ポイントをバックアップする手順について説明します。

## <a name="supported-workloads"></a>サポートされるワークロード

| ワークロード | オペレーション |
| --- | --- |
| Azure Virtual Machines (プレビュー)   <br><br>  Azure の仮想マシンにおける SQL Server   <br><br>   Azure Virtual Machines 内の SAP HANA | <ul><li> アーカイブ可能な復旧ポイントを表示する       </li><li>推奨される復旧ポイントを表示する (Virtual Machines の場合のみ)    </li><li>アーカイブ可能な復旧ポイントを移動する    </li><li>推奨される復旧ポイントを移動する (Azure Virtual Machines の場合のみ)    </li><li>アーカイブされた復旧ポイントを表示する    </li><li>アーカイブされた復旧ポイントから復元する </li></ul> |


## <a name="get-started"></a>はじめに

1. AZ CLI バージョン 2.26.0 以降をダウンロードしてアップグレードします 

   1. 初めて CLI をインストールする場合は、[こちらの手順](/cli/azure/install-azure-cli)に従ってください。
   1. 既にインストールされているバージョンをアップグレードする場合は、az --upgrade を実行します。

2. 次のコマンドを使用してログインします。

   ```azurecli
   az login
   ```

3. サブスクリプションのコンテキストを設定します。

   ```azurecli
   az account set –s <subscriptionId>
   ```
## <a name="view-archivable-recovery-points"></a>アーカイブ可能な復旧ポイントを表示する

次のコマンドを使用して、アーカイブ可能な復旧ポイントを Vault-Archive レベルに移動できます。 対象となる条件について詳しくは、[こちら](archive-tier-support.md#supported-workloads)をご覧ください。

- **Azure Virtual Machines の場合**

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureIaasVM} --workload-type {VM}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Azure Virtual Machines 内の SQL Server の場合

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Azure Virtual Machines 内の SAP HANA の場合

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

## <a name="check-why-a-recovery-point-isnt-archivable"></a>復旧ポイントをアーカイブできない理由を調べる

次のコマンドを実行します。

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload / AzureIaasVM} --workload-type {MSSQL / SAPHANA / VM}  --query [].{Name:name,move_ready:properties.recoveryPointMoveReadinessInfo.ArchivedRP.isReadyForMove,additional_details: properties.recoveryPointMoveReadinessInfo.ArchivedRP.additionalInfo
```

すべての復旧ポイントについて、アーカイブ可能かどうか、およびアーカイブ可能ではない場合の理由の一覧が表示されます

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>アーカイブ可能ポイントの推奨セットを確認する (Azure VM の場合のみ)

次のコマンドを実行します。

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --recommended-for-archive
```

推奨セットについて詳しくは、[こちら](archive-tier-support.md#archive-recommendations-only-for-azure-virtual-machines)をご覧ください。

>[!Note]
>- コストの節約額はさまざまな原因によって左右されるため、すべてのインスタンスで同じではない場合があります。
>- コスト削減が保証されるのは、推奨セットに含まれるすべての復旧ポイントを Vault-Archive レベルに移動する場合のみです。

## <a name="move-to-archive"></a>アーカイブに移動する

次のコマンドを使用して、アーカイブ可能な復旧ポイントを Vault-Archive レベルに移動できます。 コマンドの name パラメーターには、アーカイブ可能な復旧ポイントの名前を指定する必要があります。

- **Azure 仮想マシンの場合**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Azure 仮想マシン内の SQL Server の場合**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Azure 仮想マシン内の SAP HANA の場合**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```

## <a name="view-archived-recovery-points"></a>アーカイブされた復旧ポイントを表示する

次のコマンドを使用します。

- **Azure Virtual Machines の場合**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload } --workload-type {VM} -- tier {VaultArchive}
    ```
- **Azure Virtual Machines 内の SQL Server の場合**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} -- tier {VaultArchive}
    ```
- **Azure Virtual Machines 内の SAP HANA の場合**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} -- tier {VaultArchive}
    ```

## <a name="restore"></a>復元 

次のコマンドを実行します。

- **Azure Virtual Machines の場合**

    ```azurecli
    az backup restore restore-disks -g {rg} -v {vault} -c {container} -i {item} --rp-name {rp} --storage-account {storage_account} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

- **Azure VM 内の SQL Server または Azure VM 内の SAP HANA の場合**

    ```azurecli
    az backup recoveryconfig show --resource-group saphanaResourceGroup \
        --vault-name saphanaVault \
        --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
        --item-name saphanadatabase;hxe;hxe \
        --restore-mode AlternateWorkloadRestore \
        --rp-name 7660777527047692711 \
        --target-item-name restored_database \
        --target-server-name hxehost \
        --target-server-type HANAInstance \
        --workload-type SAPHANA \
        --output json


    az backup restore restore-azurewl -g {rg} -v {vault} --recovery-config {recov_config} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

::: zone-end



::: zone pivot="client-portaltier"

この記事では、Azure portal を使用して、Archive レベル、スナップショット、Standard レベルに長期保有ポイントをバックアップする手順について説明します。

## <a name="supported-workloads"></a>サポートされるワークロード

| ワークロード | オペレーション |
| --- | --- |
| Azure Virtual Machine | <ul><li>アーカイブされた復旧ポイントを表示する    </li><li>アーカイブされた復旧ポイントを復元する   </li><li>アーカイブの移動と復元のジョブを表示する </li></ul> |
| Azure Virtual Machines 内の SQL Server <br> Azure Virtual Machines 内の SAP HANA | <ul><li>アーカイブされた復旧ポイントを表示する    </li><li>すべてのアーカイブ可能な復旧をアーカイブに移動する   </li><li>アーカイブされた復旧ポイントから復元する   </li><li>アーカイブの移動と復元のジョブを表示する</li></ul> |

## <a name="view-archived-recovery-points"></a>アーカイブされた復旧ポイントを表示する

これで、アーカイブに移動されたすべての復旧ポイントを表示できるようになりました。

:::image type="content" source="./media/use-archive-tier-support/view-recovery-points-list-inline.png" alt-text="復旧ポイントの一覧を示すスクリーンショット。" lightbox="./media/use-archive-tier-support/view-recovery-points-list-expanded.png":::


## <a name="move-archivable-recovery-points-for-a-particular-sqlsap-hana-database"></a>特定の SQL または SAP HANA データベースのアーカイブ可能な復旧ポイントを移動する

これで、特定の SQL または SAP HANA データベースのすべての復旧ポイントを一度に移動できます。

次の手順に従います。

1. 復旧ポイントを Vault-Archive レベルに移動するバックアップ項目 (Azure VM 内の SQL Server または SAP HANA 内のデータベース) を選びます。

2. 7 日より古い復旧ポイントを表示するには、 **[ここをクリック]** を選びます。

   :::image type="content" source="./media/use-archive-tier-support/view-old-recovery-points-inline.png" alt-text="7 日より古い復旧ポイントを表示するプロセスを示すスクリーンショット。" lightbox="./media/use-archive-tier-support/view-old-recovery-points-expanded.png":::

3. アーカイブに移動する対象となるすべてのアーカイブ可能なポイントを表示するには、 _[Long term retention points can be moved to archive. To move all ‘eligible recovery points’ to archive tier, click here]\(長期保有ポイントをアーカイブに移動できます。すべての "対象となる復旧ポイント" を Archive レベルに移動するには、ここをクリックしてください\)_ を選びます。

   :::image type="content" source="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-inline.png" alt-text="アーカイブへの移動の対象となるアーカイブ可能なすべてのポイントを表示するプロセスを示すスクリーンショット。" lightbox="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-expanded.png":::

   すべてのアーカイブ可能な復旧ポイントが表示されます。


   対象となる条件について詳しくは、[こちら](archive-tier-support.md#supported-workloads)をご覧ください。

3. **[Move Recovery Points to archive]\(復旧ポイントをアーカイブに移動する\)** をクリックして、すべての復旧ポイントを Vault-Archive レベルに移動します。

   :::image type="content" source="./media/use-archive-tier-support/move-all-recovery-points-to-vault-inline.png" alt-text="Vault-Archive レベルへのすべての復旧ポイントの移動プロセスを開始するオプションを示すスクリーンショット。" lightbox="./media/use-archive-tier-support/move-all-recovery-points-to-vault-expanded.png":::

   >[!Note]
   >このオプションを選ぶと、アーカイブ可能なすべての復旧ポイントが Vault-Archive に移動されます。

バックアップ ジョブの進行状況を監視できます。

## <a name="restore"></a>復元

アーカイブに移動された復旧ポイントを復元するには、リハイドレーション期間とリハイドレーションの優先度に必要なパラメーターを追加する必要があります。

:::image type="content" source="./media/use-archive-tier-support/restore-in-portal.png" alt-text="ポータルで復旧ポイントを復元するプロセスを示すスクリーンショット。":::

## <a name="view-jobs"></a>ジョブの表示

:::image type="content" source="./media/use-archive-tier-support/view-jobs-portal.png" alt-text="ポータルでジョブを表示するプロセスを示すスクリーンショット。":::

## <a name="view-archive-usage-in-vault-dashboard"></a>コンテナー ダッシュボードでアーカイブの使用状況を表示する

コンテナー ダッシュボードでアーカイブの使用状況を表示することもできます。

:::image type="content" source="./media/use-archive-tier-support/view-archive-usage-in-vault-dashboard.png" alt-text="コンテナー ダッシュボードでのアーカイブの使用状況を示すスクリーンショット。":::


::: zone-end

## <a name="next-steps"></a>次の手順

- [Archive レベルのエラーのトラブルシューティング](troubleshoot-archive-tier.md)
