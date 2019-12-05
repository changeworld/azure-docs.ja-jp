---
title: マネージド インスタンス - ポイントインタイム リストア
description: マネージド インスタンス内の SQL データベースを以前の特定の時点に復元します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707658"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>マネージド インスタンス内の SQL データベースを以前の特定の時点に復元する

ポイントインタイム リストア (PITR) を使用して、データベースを、過去のある時点における別のデータベースのコピーとして作成します。 この記事では、Azure SQL Database マネージド インスタンス内のデータベースについてポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアは、エラーによって発生したインシデント、データの誤った読み込み、重要なデータの削除などの復旧のシナリオで役に立ちます。 単にテストや監査のために使用することもできます。 データベースの設定によって異なりますが、バックアップ ファイルは 7 日から 35 日間保持されます。

ポイントインタイム リストアでは次のことができます。

- 既存のデータベースからデータベースを復元する。
- 削除されたデータベースからデータベースを復元する。

マネージド インスタンスでは、ポイントインタイム リストアで次のことも行うことができます。

- 同じマネージド インスタンスにデータベースを復元する。
- 別のマネージド インスタンスにデータベースを復元する。

> [!NOTE]
> マネージド インスタンス全体のポイントインタイム リストアは実行できません。 この記事では、実行できること (マネージド インスタンス上でホストされているデータベースのポイントインタイム リストア) についてのみ説明します。

## <a name="limitations"></a>制限事項

マネージド インスタンス間で復元する場合は、両方のインスタンスが同じサブスクリプションとリージョン内に存在する必要があります。 リージョン間およびサブスクリプション間での復元は、現在、サポートされていません。

> [!WARNING]
> マネージド インスタンスのストレージ サイズに注意してください。 復元するデータのサイズによっては、インスタンス ストレージが不足する可能性があります。 復元されたデータ用の領域が不足している場合は、別の方法を使用してください。

次の表に、マネージド インスタンスにおけるポイントインタイム リストアのシナリオを示します。

|           |同じマネージド インスタンスに既存の DB を復元する| 別のマネージド インスタンスに既存の DB を復元する|同じマネージド インスタンスに削除された DB を復元する|別のマネージド インスタンスに削除された DB を復元する|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| はい|いいえ |いいえ|いいえ|
|**Azure CLI**|はい |はい |いいえ|いいえ|
|**PowerShell**| はい|はい |はい|はい|

## <a name="restore-an-existing-database"></a>既存のデータベースを復元する

Azure portal、PowerShell、または Azure CLI を使用することで、既存のデータベースを同じインスタンスに復元することができます。 データベースを別のインスタンスに復元するには、ターゲット マネージド インスタンスとリソース グループのプロパティを指定できるように、PowerShell または Azure CLI を使用します。 これらのパラメーターを指定しないと、既定では、データベースが既存のインスタンスに復元されます。 Azure portal では、現在のところ別のインスタンスへの復元はサポートされていません。

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. ご利用のマネージド インスタンスに移動し、復元するデータベースを選択します。
3. データベース ページで **[復元]** を選択します。

    ![Azure portal を使用してデータベースを復元する](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. **[復元]** ページで、データベースの復元先とする日付と時刻のポイントを選択します。
5. **[確認]** を選択して、ご利用のデータベースを復元します。 このアクションにより、復元プロセスが開始されます。このプロセスでは、新しいデータベースが作成され、指定した時点における元のデータベースのデータが入力されます。 復旧プロセスの詳細については、「[復旧時間](sql-database-recovery-using-backups.md#recovery-time)」を参照してください。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell がまだインストールされていない場合は、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

PowerShell を使用してデータベースを復元するには、次のコマンドでパラメーターの値を指定します。 次に、次のコマンドを実行します。

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

データベースを別のマネージド インスタンスに復元するには、ターゲット リソース グループとターゲット マネージド インスタンスの名前も指定します。  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

詳細については、[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) を参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

Azure CLI を使用してデータベースを復元するには、次のコマンドでパラメーターの値を指定します。 次に、次のコマンドを実行します。

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

データベースを別のマネージド インスタンスに復元するには、ターゲット リソース グループとターゲット マネージド インスタンスの名前も指定します。  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

使用可能なパラメーターの詳細については、[マネージド インスタンスでのデータベースの復元に関する CLI のドキュメント](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)を参照してください。

---

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元

削除されたデータベースの復元は、PowerShell を使用することでのみ行うことができます。 データベースは、同じインスタンスまたは別のインスタンスに復元できます。

PowerShell を使用して削除されたデータベースを復元するには、次のコマンドでパラメーターの値を指定します。 次に、次のコマンドを実行します。

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

削除されたデータベースを別のインスタンスに復元するには、リソース グループとマネージド インスタンスの名前を変更します。 また、location パラメーターが、リソース グループおよびマネージド インスタンスの場所と一致することも確認してください。

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>既存のデータベースを上書きする

既存のデータベースを上書きするには、次のことを行う必要があります。

1. 上書きする既存のデータベースを削除します。
2. ポイントインタイム リストアされたデータベースの名前を、削除したデータベースの名前に変更します。

### <a name="drop-the-original-database"></a>元のデータベースを削除する

Azure portal、PowerShell、または Azure CLI を使って、データベースを削除できます。

また、マネージド インスタンスに直接接続し、SQL Server Management Studio (SSMS) を起動し、次の Transact-SQL (T-SQL) コマンドを実行して、データベースを削除することもできます。

```sql
DROP DATABASE WorldWideImporters;
```

マネージド インスタンス内のデータベースに接続するには、次のいずれかの方法を使用します。

- [Azure 仮想マシンを使用した SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、マネージド インスタンスからデータベースを選択し、 **[削除]** を選択します。

   ![Azure portal を使ってデータベースを削除する](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell コマンドを使用して、マネージド インスタンスから既存のデータベースを削除します。

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

次の Azure CLI コマンドを使用して、マネージド インスタンスから既存のデータベースを削除します。

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>元のデータベース名と一致するように新しいデータベース名を変更する

マネージド インスタンスに直接接続し、SQL Server Management Studio を起動します。 その後、次の Transact-SQL (T-SQL) クエリを実行します。 このクエリは、復元されたデータベースの名前を、上書きする予定の削除されたデータベースの名前に変更します。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

マネージド インスタンス内のデータベースに接続するには、次のいずれかの方法を使用します。

- [Azure 仮想マシン](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [ポイント対サイト](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [パブリック エンドポイント](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>次の手順

[自動化されたバックアップ](sql-database-automated-backups.md)について確認します。
