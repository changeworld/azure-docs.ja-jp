---
title: Azure PowerShell を使用してストレージ アカウント内の Azure BLOB をバックアップする
description: Azure PowerShell を使用して、ストレージ アカウント内のすべての Azure BLOB をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: 0f3e7d03abbf4a5bfbb5d5cb533df04a17c71edf
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179782"
---
# <a name="back-up-all-azure-blobs-in-a-storage-account-using-azure-powershell"></a>Azure PowerShell を使用してストレージ アカウント内のすべての Azure BLOB をバックアップする

この記事では、Azure PowerShell を使用して、ストレージ アカウント内のすべての [Azure BLOB](./blob-backup-overview.md) をバックアップする方法について説明します。

この記事では、次の方法について学習します。

- 開始する前に

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- ストレージ アカウント内のすべての Azure BLOB のバックアップの構成

Azure BLOB の使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](blob-backup-support-matrix.md)に関するページをご覧ください。

> [!IMPORTANT]
> Azure BLOB のサポートは、Az 5.9.0 バージョンから利用できます。

## <a name="before-you-start"></a>開始する前に

始める前に[前提条件](./blob-backup-configure-manage.md#before-you-start)と[サポート マトリックス](./blob-backup-support-matrix.md)に関する記事を参照してください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバー、Azure BLOB、および Azure BLOB など、Azure Backup によってサポートされるさまざまな新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

バックアップ コンテナーを作成する前に、コンテナー内のデータのストレージ冗長を選択します。 次に、そのストレージ冗長と場所を使用したバックアップ コンテナーの作成に進みます。 この記事では、リソース グループ _testBkpVaultRG_ の _westus_ リージョンに、バックアップ コンテナー _TestBkpVault_ を作成します。 [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.9.0&preserve-view=true) コマンドを使用してバックアップ コンテナーを作成します。詳細については、[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)に関する記事を参照してください。

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore

New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

コンテナーを作成したら、Azure BLOB を保護するためのバックアップ ポリシーを作成しましょう。

> [!IMPORTANT]
> コンテナーのバックアップ ストレージの冗長性が表示されますが、冗長性は BLOB の運用バックアップには適用されません。このバックアップはローカルなものであり、バックアップ コンテナーにデータは格納されないためです。 ここでのバックアップ コンテナーは、ストレージ アカウント内のブロック BLOB の保護を管理するために役立つ管理エンティティです。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

> [!IMPORTANT]
> ポリシーの作成と Azure BLOB のバックアップの構成に進む前に、[こちらのセクション](blob-backup-configure-manage.md#before-you-start)をお読みください。

Azure BLOB バックアップのバックアップ ポリシーの内部構成要素を理解するには、[Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.9.0&preserve-view=true) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureBlob
$policyDefn | fl


DatasourceType : {Microsoft.Storage/storageAccounts/blobServices}
ObjectType     : BackupPolicy
PolicyRule     : {Default}

$policyDefn.PolicyRule | fl

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api202101.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

このポリシー テンプレートは、ライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) のみで構成されます。 BLOB の運用バックアップは継続的に行われるため、バックアップを実行するスケジュールは必要ありません。

```azurepowershell-interactive
$policyDefn.PolicyRule.Lifecycle | fl


DeleteAfterDuration        : P30D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

> [!NOTE]
> 長期間を対象にした復元の場合、復元操作の完了に時間がかかることがあります。 また、一連のデータの復元にかかる時間は、復元期間中に行われた書き込み操作と削除操作の数に基づきます。 たとえば、100 万オブジェクトを持つアカウントで毎日 3,000 オブジェクトが追加され、毎日 1,000 オブジェクトが削除される場合、過去 30 日間のポイントまで復元するのに約 2 時間必要になります。<br><br>この変更率のアカウントでは、過去 90 日以上の保有期間と復元は推奨されません。

ポリシー オブジェクトに目的の値をすべて設定したら、[New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.9.0&preserve-view=true) コマンドを使用したポリシー オブジェクトからの新しいポリシーの作成に進みます。

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name blobBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
blobBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$blobBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "blobBkpPolicy"
```

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、ストレージ アカウント内のすべての Azure BLOB を保護するためにユーザーが考慮する必要がある重要なポイントが 2 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>保護する BLOB が含まれるストレージ アカウント

保護する BLOB が含まれるストレージ アカウントの Azure Resource Manager ID を取得します。 これは、ストレージ アカウントの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ _blobrg_ の下にある、_PSTestSA_ という名前のストレージ アカウントを例に説明します。

```azurepowershell-interactive
$SAId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/PSTestSA"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーからストレージ アカウント内に存在する BLOB のバックアップを有効にするには、ストレージ アカウントに対するアクセス許可が付与されている必要があります。 コンテナーのシステム割り当てマネージド ID は、そのようなアクセス許可を割り当てるために使用されます。

### <a name="assign-permissions"></a>アクセス許可の割り当て

コンテナー (コンテナー MSI と表示) と関連するストレージ アカウントに、RBAC 経由でいくつかのアクセス許可を割り当てる必要があります。 これらは、ポータルまたは PowerShell を使用して実行できます。 すべての[関連するアクセス許可](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)については、こちらをご覧ください。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。 まず、[Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) コマンドを使用して、関連するコンテナー、ポリシー、ストレージ アカウントを使用し、関連する要求を準備します。 次に、[New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) コマンドを使用して、ストレージ アカウント内の BLOB を保護する要求を送信します。

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureBlob -DatasourceLocation $TestBkpvault.Location -PolicyId $blobBkpPol[0].Id -DatasourceId $SAId 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166
```

> [!IMPORTANT]
> BLOB のバックアップ用にストレージ アカウントを構成すると、変更フィードや削除ロックなど、いくつかの機能が影響を受けます。 [詳細については、こちらを参照してください](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)。

## <a name="next-steps"></a>次のステップ

[Azure PowerShell を使用して Azure BLOB を復元する](restore-blobs-storage-account-ps.md)