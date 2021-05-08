---
title: Azure PowerShell によるマネージド ディスクの復元
description: Azure PowerShell を使用して、Azure マネージド ディスクを復元する方法について説明します。
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520039"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell を使用した、Azure マネージド ディスクの復元

この記事では、Azure Backup によって作成された復元ポイントから [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)を復元する方法について説明します。

現在、バックアップが作成された既存のソース ディスクを置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。 復旧ポイントから復元して、バックアップが作成されたソース ディスクと同じリソース グループか、または他の任意のリソース グループに新しいディスクを作成できます。 これは、別の場所への復旧 (ALR) と呼ばれ、ソース ディスクと復元された (新しい) ディスクの両方を保持するのに役立ちます。

この記事では、次の方法について学習します。

- 復元して新しいディスクを作成する

- 復元操作の状態を追跡する

この例では、リソースグループ "testBkpVaultRG" の下にある既存のバックアップ コンテナー "testBkpVault" を参照します

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>復元して新しいディスクを作成する

### <a name="setting-up-permissions"></a>アクセス許可の設定

バックアップ コンテナーでは、マネージド ID を使用して他の Azure リソースにアクセスします。 バックアップから復元するには、バックアップ コンテナーのマネージド ID に、ディスクを復元する先のリソース グループに対する一連のアクセス許可が必要になります。

バックアップ コンテナーでは、リソースあたり 1 つに制限され、このリソースのライフサイクルに関連付けられているシステム割り当てマネージド ID を使用します。 マネージド ID には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してアクセス許可を付与できます。 マネージド ID は、Azure リソースでのみ使用できる特殊な種類のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

[ここ](restore-managed-disks.md#restore-to-create-a-new-disk)に記載されているように、ディスクを復元または作成するターゲット リソース グループで、コンテナーのシステム割り当て済みマネージド ID に適切なアクセス許可を割り当てます。

### <a name="fetching-the-relevant-recovery-point"></a>適切な復元ポイントのフェッチ

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用してすべてのインスタンスをフェッチし、該当するインスタンスを特定します。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az.Resourcegraph** および [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用して、多くのコンテナーとサブスクリプション内のインスタンスにまたがる検索を行うこともできます。

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

インスタンスが特定されたら、関連する復元ポイントをフェッチします。

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>復元要求の準備

[上記](#setting-up-permissions)の説明にあるアクセス許可と、必要なディスク名が割り当てられているターゲット リソース グループを使用して、作成される新しいディスクの ARM ID を作成します。 たとえば、別のサブスクリプションを使用して、リソース グループ **targetrg** の下で、ディスクに **PSTestDisk2** という名前を付けることができます。

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

関連するすべての詳細を含む復元要求を準備するには、[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>復元のトリガー

前に準備した要求で復元をトリガーするには、[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>ジョブの追跡

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

**Az.ResourceGraph** を使用して、すべてのバックアップ コンテナーにわたるすべてのジョブを追跡することもできます。 関連するジョブを取得するには、[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用します。これを、バックアップ コンテナー全体にまたがって行うことも可能です。

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>次の手順

- [Azure ディスク バックアップに関する FAQ](disk-backup-faq.yml)
