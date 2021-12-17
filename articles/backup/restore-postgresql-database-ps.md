---
title: Azure PostgreSQL データベースを復元するには、Azure PowerShell
description: Azure PowerShell を使用して Azure PostgreSQL データベースを復元する方法を説明します。
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c5a17d1e0969dda584c19f344e226dff666b6bea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716716"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell を使用して Azure PostgreSQL データベースを復元する

この記事では、Azure Backup によってバックアップされた Azure PostgreSQL サーバーに、[Azure PostgreSQL データベース](../postgresql/overview.md#azure-database-for-postgresql---single-server)を復元する方法について説明します。

PaaS データベースの場合、既存のデータベースを (バックアップが作成された場所から) 置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。 復旧ポイントから復元して、同じ Azure PostgreSQL サーバーまたは他の PostgreSQL サーバーに新しいデータベースを作成できます。 これは、Alternate-Location Recovery (ALR) と呼ばれ、ソース データベースと復元された (新しい) データベースの両方を保持するのに役立ちます。

この記事では、次の方法について学習します。

- PostgreSQL データベースを復元して新しく作成する

- 復元操作の状態を追跡する

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>PostgreSQL データベースを復元して新しく作成する

### <a name="set-up-permissions"></a>アクセス許可の設定

バックアップボールトは、マネージド IDを使用して他のAzureリソースにアクセスします。 バックアップから復元するには、バックアップボールトのマネージド ID に、データベースを復元する Azure PostgreSQL サーバーに対する一連のアクセス許可が必要です。

ターゲット PostgreSQL サーバーでコンテナーのシステム割り当てマネージド ID に関連するアクセス許可を割り当てるには、[Azure PostgreSQL データベースをバックアップするために必要な一連のアクセス許可](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)に関する記事を参照してください。

回復ポイントをファイルとしてストレージ アカウントに復元するには、[バックアップ コンテナーのシステム割り当てマネージド ID がターゲット ストレージ アカウントにアクセスできる必要](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)があります。

### <a name="fetching-the-relevant-recovery-point"></a>適切な復元ポイントのフェッチ

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) コマンドを使用してすべてのインスタンスをフェッチし、該当するインスタンスを特定します。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az.Resourcegraph** および [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用して、多くのコンテナーとサブスクリプション内のインスタンスにまたがる回復ポイントを検索することもできます。

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

検索条件をフィルター処理するには、次に示すように PowerShell クライアント検索機能を使用します。

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

インスタンスが特定されたら、関連する復元ポイントをフェッチします。

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

アーカイブ層から復旧ポイントをフェッチする必要がある場合は、次のようにクライアント フィルターを追加します。

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>復元要求を準備する

PostgreSQL データベースにはさまざまな復元オプションがあります。 回復ポイントを別のデータベースとして復元したり、ファイルとして復元したりすることができます。 復旧ポイントはアーカイブ アクセス層にすることもできます。

#### <a name="restore-as-database"></a>データベースとして復元

作成する新しい PostgreSQL データベースの Azure Resource Managed ID (ARM ID) を構築します ([上記](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたターゲット PostgreSQL サーバーと、必要な PostgreSQL データベース名を指定します)。 たとえば、異なるサブスクリプションを持つリソース グループ **targetrg** 内のターゲット PostgreSQL サーバー **targetossserver** の下で、PostgreSQL データベースに **emprestored21** という名前を付けられます。

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

関連するすべての詳細を含む復元要求を準備するには、[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

アーカイブ ベースの復旧ポイントの場合は、次の手順を実行する必要があります。

1. アーカイブ データストアからボールトストアへのリヒドレートする。
1. ソース データストアを変更します。
1. リハイドレートの優先度を指定するための他のパラメーターを追加します。
1. リハイドレートされた復旧ポイントをコンテナー データ ストアに保持する期間を指定します。
1. この復旧ポイントからデータベースとして復元します。

上記のすべての操作に対する要求を一度に準備するには、次のコマンドを使用します。

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>ファイルとして復元

[上](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたストレージ アカウント内のコンテナーの URI をフェッチします。 たとえば、異なるサブスクリプションを持つストレージ アカウント **testossstorageaccount** の下で、コンテナーに **testcontainerrestore** という名前を付けられます。

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

関連するすべての詳細を含む復元要求を準備するには、[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

アーカイブベースの復旧ポイントの場合は、ソース データストアを変更し、リハイドレートの優先度と、リハイドレートされた復旧ポイントのリハイドレート期間 (日数) を以下に示すように追加します。

```azurepowershell-interactive
$OssRestoreAsFilesFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" -RehydrationDuration "14" -RehydrationPriority Standard
```

### <a name="trigger-the-restore"></a>復元のトリガー

前に準備した要求で復元をトリガーするには、[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $OssRestoreReq
```

## <a name="tracking-job"></a>ジョブの追跡

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

*Az.ResourceGraph* を使用して、すべての Backup コンテナーを対象に、全ジョブを追跡することもできます。 関連するジョブを取得し、バックアップ コンテナー全体にまたがる場合にも、[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>次の手順

- [Azure PostgreSQL Backup の概要](backup-azure-database-postgresql-overview.md)