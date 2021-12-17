---
title: Azure PowerShell を使用して Azure BLOB を復元する
description: Azure PowerShell を使用して任意の時点に Azure BLOB を復元する方法について説明します。
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 653c3ce6fbfea4d890c3dc3e52fb1032a72b7eab
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803064"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-powershell"></a>Azure PowerShell を使用して任意の時点に Azure BLOB を復元する

この記事では、Azure Backup を使用して [BLOB](blob-backup-overview.md) を任意の時点に復元する方法について説明します。

> [!IMPORTANT]
> Azure BLOB のサポートは、Az 5.9.0 バージョンから利用できます。

> [!IMPORTANT]
> Azure Backup を使用した Azure BLOB の復元に進む前に、[重要なポイント](blob-restore.md#before-you-start)を確認してください。

この記事では、次の方法について学習します。

- Azure BLOB を任意の時点に復元する

- 復元操作の状態を追跡する

この例では、リソースグループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restoring-azure-blobs-within-a-storage-account"></a>ストレージ アカウント内での Azure BLOB の復元

### <a name="fetching-the-valid-time-range-for-restore"></a>復元の有効な時間範囲をフェッチする

BLOB の運用バックアップは継続的に行うので、注意すべき復元元ポイントはありません。 代わりに、BLOB を任意の時点に復元できる有効な時間範囲をフェッチする必要があります。 この例では、過去 30 日以内に復元する有効な時間範囲を確認しましょう。

```azurepowershell-interactive
$startDate = (Get-Date).AddDays(-30)
$endDate = Get-Date
```

まず [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) コマンドを使用してすべてのインスタンスをフェッチし、該当するインスタンスを特定します。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Az.Resourcegraph および [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.9.0&preserve-view=true) コマンドを使用して、多くのコンテナーとサブスクリプション内のインスタンスにまたがる検索を行うこともできます。

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureBlob -ProtectionStatus ProtectionConfigured
```

インスタンスが特定されたら、[Find-AzDataProtectionRestorableTimeRange](/powershell/module/az.dataprotection/find-azdataprotectionrestorabletimerange) コマンドを使用して、関連する復旧範囲をフェッチします。

```azurepowershell-interactive
Find-AzDataProtectionRestorableTimeRange -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName -StartTime $startDate -endTime $endDate

EndTime    : 2021-04-24T08:57:36.4149422Z
ObjectType : RestorableTimeRange
StartTime  : 2021-03-25T14:27:31.0000000Z

$DesiredPIT = (Get-Date -Date "2021-04-23T02:47:02.9500000Z")
```

### <a name="preparing-the-restore-request"></a>復元要求の準備

復元する任意の時点が固定されると、復元するオプションが複数あります。 関連するすべての詳細を含む復元要求を準備するには、[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.9.0&preserve-view=true) コマンドを使用します。

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>任意の時点へすべての BLOB を復元する

このオプションを使用すると、ストレージ アカウント内のすべてのブロック BLOB が、選択した時点までロールバックすることで復元されます。 大量のデータが含まれている、または高チャーンが監視されているストレージ アカウントの場合、復元に時間がかかることがあります。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2]
```

#### <a name="restoring-selected-containers"></a>選択したコンテナーを復元する

このオプションを使用すると、復元対象のコンテナーを最大 10 個まで参照して選択できます。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -ContainersList "abc","xyz"
```

#### <a name="restoring-containers-using-a-prefix-match"></a>プレフィックスの一致を使用してコンテナーを復元する

このオプションを使用すると、プレフィックスの一致を使用して BLOB のサブセットを復元できます。 単一のコンテナー内または複数のコンテナー全体の BLOB の最大 10 個の辞書式範囲を復元して、以前の特定の時点の状態にそれらの BLOB を戻すことができます。 次の点に注意してください。

- コンテナー名と BLOB プレフィックスを区切るには、スラッシュ (/) を使用できます
- 指定した範囲の開始は範囲に含まれますが、指定した範囲の終了は含まれません。

プレフィックスを使用して BLOB 範囲を復元する方法の[詳細を確認](blob-restore.md#use-prefix-match-for-restoring-blobs)します。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -FromPrefixPattern "containerabc/aaa","containerabc/ccc" -ToPrefixPattern "containerabc/bbb","containerabc/ddd"
```

### <a name="trigger-the-restore"></a>復元のトリガー

前に準備した要求で復元をトリガーするには、[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.9.0&preserve-view=true) コマンドを使用します。

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>ジョブの追跡

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.9.0&preserve-view=true) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

Az.ResourceGraph を使用して、すべてのバックアップ コンテナーにわたるすべてのジョブを追跡することもできます。 関連するジョブを取得するには、[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.9.0&preserve-view=true) コマンドを使用します。これを、バックアップ コンテナー全体にまたがって行うことも可能です。

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureBlob -Operation Restore
```

## <a name="next-steps"></a>次のステップ

[Azure BLOB バックアップの概要](blob-backup-overview.md)
