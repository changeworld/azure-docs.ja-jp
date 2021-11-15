---
title: Azure SQL Database:長期のバックアップ リテンション期間の管理
description: Azure portal、Azure CLI、PowerShell を使用して、Azure SQL Database の自動バックアップを Azure ストレージに (最大 10 年間) 格納および復元する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 12/16/2020
ms.openlocfilehash: 72f92c1659497cda8e4f595df17899a4367d00b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236246"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database の長期的なバックアップ保有期間を管理する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database を使用すると、バックアップが別々の Azure Blob Storage コンテナーに最長 10 年間自動的に保持されるように、[長期的なバックアップ保有期間](long-term-retention-overview.md)ポリシー (LTR) を設定できます。 Azure portal、Azure CLI、または PowerShell でこのようなバックアップを使用して、データベースを復旧できます。 長期保有ポリシーは、[Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) でもサポートされています。

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/portal)

有効な Azure サブスクリプション

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の環境を準備します。

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell のための環境を準備します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

**Get-AzSqlDatabaseLongTermRetentionBackup** および **Restore-AzSqlDatabase** の場合、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- SQL Server 共同作成者ロールまたは
- 次のアクセス許可を持つカスタム ロール:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

**Remove-AzSqlDatabaseLongTermRetentionBackup** の場合、ロールには、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- 次のアクセス許可を持つカスタム ロール:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> SQL Server の共同作成者ロールには、LTR バックアップを削除するアクセス許可はありません。

Azure RBAC のアクセス許可は、"*サブスクリプション*" または "*リソース グループ*" のいずれかのスコープで付与できます。 ただし、削除されたサーバーに属する LTR バックアップにアクセスするには、そのサーバーの *サブスクリプション* スコープにアクセス許可を付与する必要があります。

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

---

## <a name="create-long-term-retention-policies"></a>長期保有ポリシーを作成する

# <a name="portal"></a>[ポータル](#tab/portal)

ご利用のサービス レベルのリテンション期間より長く[自動バックアップを保持](long-term-retention-overview.md)するように SQL Database を構成できます。

1. Azure portal で、ご自分のサーバーに移動してから、 **[バックアップ]** を選択します。 **[保持ポリシー]** タブを選択して、バックアップの保持設定を変更します。

   ![保持ポリシーのエクスペリエンス](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. [保持ポリシー] タブで、長期的なバックアップ保持ポリシーを設定または変更するデータベースを選択します。 選択されていないデータベースへの影響はありません。

   ![バックアップ保持ポリシーを構成するデータベースを選択する](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. **[ポリシーの構成]** ウィンドウで、週単位、月単位、または年単位のバックアップに必要な保有期間を指定します。 長期的なバックアップ保有期間を設定しないことを示すには、保有期間を "0" にします。

   ![ポリシーの構成ウィンドウ](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. **[適用]** を選択して、指定した保有期間設定を選択したすべてのデータベースに適用します。

> [!IMPORTANT]
> 長期的なバックアップ保有期間ポリシーを有効にすると、初回バックアップが表示されて復元に利用できる状態になるまでに、最大 7 日かかる場合があります。 LTR バックアップ周期の詳細については、[長期的なバックアップ保有期間](long-term-retention-overview.md)に関するページを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

LTR ポリシーを作成するには、[az sql db ltr-policy set](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_set) コマンドを実行します。 次の例では、週単位のバックアップについて 12 週間の長期保有ポリシーを設定します。

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W"
```

この例では、週単位のバックアップについては 12 週間、年単位のバックアップについては 5 年、年単位の LTR バックアップを取得するのは 4 月 15 日の週に設定します。

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W" \
   --yearly-retention "P5Y" \
   --week-of-year 16
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

---

## <a name="view-backups-and-restore-from-a-backup"></a>バックアップを表示してバックアップから復元する

LTR ポリシーを使用して保持されている特定のデータベースのバックアップを表示し、それらのバックアップから復元します。

# <a name="portal"></a>[ポータル](#tab/portal)

1. Azure portal で、ご自分のサーバーに移動してから、 **[バックアップ]** を選択します。 特定のデータベースに使用可能な LTR バックアップを表示するには、[使用可能な LTR バックアップ] 列の下にある **[管理]** を選択します。 選択したデータベースに使用可能な LTR バックアップの一覧を示すウィンドウが表示されます。

   ![使用可能なバックアップのエクスペリエンス](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. 表示されている **[使用可能な LTR バックアップ]** ウィンドウで、使用可能なバックアップを確認します。 復元元のバックアップまたは削除するバックアップを選択できます。

   ![使用可能な LTR バックアップを表示する](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. 使用可能な LTR バックアップから復元するには、復元元のバックアップを選択してから、 **[復元]** を選択します。

   ![使用可能な LTR バックアップから復元する](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. 新しいデータベースの名前を選択してから、 **[確認と作成]** を選択して、復元の詳細を確認します。 **[作成]** を選択して、選択したバックアップからデータベースを復元します。

   ![復元の詳細を構成する](./media/long-term-backup-retention-configure/restore-ltr.png)

1. ツール バーの通知アイコンを選択して、復元ジョブの状態を確認します。

   ![復元ジョブの進行状況](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 復元ジョブが完了したら、 **[SQL データベース]** ページを開き、新しく復元されたデータベースを確認します。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](recovery-using-backups.md#point-in-time-restore)できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-ltr-policies"></a>LTR ポリシーを表示する

サーバーの 1 つのデータベースの LTR ポリシーを表示するには、[az sql db ltr-policy show](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_show) コマンドを実行します。

```azurecli
az sql db ltr-policy show \
    --resource-group mygroup \
    --server myserver \
    --name mydb
```

### <a name="view-ltr-backups"></a>LTR バックアップを表示する

データベースの LTR バックアップの一覧を表示するには、[az sql db ltr-backup list](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list) コマンドを使用します。 このコマンドを使用すると、他のコマンドで使用するための `name` パラメーターを知ることができます。

```azurecli
az sql db ltr-backup list \
   --location eastus2 \
   --server myserver \
   --database mydb
```

### <a name="delete-ltr-backups"></a>LTR バックアップを削除する

LTR バックアップを削除するには、[az sql db ltr-backup delete](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_delete) コマンドを実行します。 [az sql db ltr-backup list](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list) を使用すると、バックアップの `name` を確認できます。

```azurecli
az sql db ltr-backup delete \
   --location eastus2 \
   --server myserver \
   --database mydb \
   --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000"
```

> [!IMPORTANT]
> LTR バックアップの削除は、元に戻せません。 サーバーが削除された後に LTR バックアップを削除するには、サブスクリプション スコープのアクセス許可が必要です。 Azure Monitor では、"長期保有バックアップを削除します" という操作をフィルター処理することで、それぞれの削除に関する通知を設定できます。 アクティビティ ログには、どのユーザーがいつ要求を行ったかに関する情報が含まれています。 詳しい手順については、[アクティビティ ログ アラートの作成](../../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

### <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する

LTR バックアップからデータベースを復元するには、[az sql db ltr-backup restore](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_restore) コマンドを実行します。 [az sql db ltr-backup show](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_show) を実行すると、`backup-id` を取得できます。

1. 後で使用するには、"az sql db ltr-backup show" コマンドで `backup-id` の変数を作成します。

   ```azurecli
   get_backup_id=$(az sql db ltr-backup show 
       --location eastus2 \
       --server myserver \
       --database mydb \
       --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000" \
       --query 'id' \
       --output tsv)
   ```

2. LTR バックアップからデータベースを復元します。

    ```azurecli
    az sql db ltr-backup restore \
       --dest-database targetdb \
       --dest-server myserver \
       --dest-resource-group mygroup \
       --backup-id $get_backup_id
    ```

> [!IMPORTANT]
> サーバーまたはリソース グループが削除された後に LTR バックアップから復元するには、サーバーのサブスクリプションをスコープとしたアクセス許可が必要であり、そのサブスクリプションがアクティブである必要があります。 また、オプションの -ResourceGroupName パラメーターを省略する必要もあります。

> [!NOTE]
> ここからは、SQL Server Management Studio を使って復元されたデータベースに接続し、データベースのスワップなど、必要なタスクを実行できます。 [ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="view-ltr-policies"></a>LTR ポリシーを表示する

この例では、サーバー内の LTR ポリシーの一覧を表示する方法を示します。

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>LTR ポリシーをクリアする

この例では、データベースから LTR ポリシーを消去する方法を示します。

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR バックアップを表示する

サーバー内の LTR バックアップを一覧表示する例を次に示します。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR バックアップを削除する

バックアップの一覧から LTR バックアップを削除する例を次に示します。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR バックアップの削除は、元に戻せません。 サーバーが削除された後に LTR バックアップを削除するには、サブスクリプション スコープのアクセス許可が必要です。 Azure Monitor では、"長期保有バックアップを削除します" という操作をフィルター処理することで、それぞれの削除に関する通知を設定できます。 アクティビティ ログには、どのユーザーがいつ要求を行ったかに関する情報が含まれています。 詳しい手順については、[アクティビティ ログ アラートの作成](../../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

### <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する

LTR バックアップから復元する例を次に示します。 このインターフェイスは変更されませんでしたが、リソース ID パラメーターでは LTR バックアップ リソース ID が必須になりました。

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> サーバーまたはリソース グループが削除された後に LTR バックアップから復元するには、サーバーのサブスクリプションをスコープとしたアクセス許可が必要であり、そのサブスクリプションがアクティブである必要があります。 また、オプションの -ResourceGroupName パラメーターを省略する必要もあります。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したりできます。 [ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)をご覧ください。

---

## <a name="limitations"></a>制限事項
- LTR バックアップから復元する場合、読み取りスケール プロパティは無効になります。 復元されたデータベースの読み取りスケールを有効にするには、データベースが作成された後にデータベースを更新します。
- データベースがエラスティック プール内にあるときに作成された LTR バックアップから復元する場合は、ターゲットのサービス レベル目標を指定する必要があります。 

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](automated-backups-overview.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](long-term-retention-overview.md)に関する記事を参照してください。
