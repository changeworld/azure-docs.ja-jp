---
title: "Azure SQL Database の単一データベース | Microsoft Docs"
description: "単一の Azure SQL Database のサービス レベル、パフォーマンス レベル、ストレージ量を管理します。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 12/14/2017
ms.author: carlrab
ms.openlocfilehash: 0f88b09c342c1849a5c61fdb5dc048d7cbadc83b
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Azure SQL Database で単一データベースのリソースを管理する

単一データベースでは、サービス レベル、パフォーマンス レベル、必要なストレージ量でワークロードを処理するためにデータベースで必要なリソースの量を決定します。 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Azure Portal を使って単一データベース リソースを管理する

Azure Portal を使用して、新規または既存の Azure SQL データベースのサービス レベル、パフォーマンス レベル、またはストレージ容量を設定または変更するには、次のスクリーンショットに示すように、**[価格レベル (DTU のスケール)]** をクリックし、データベースの **[パフォーマンスの構成]** ウィンドウを開きます。 

- ワークロードのサービス レベルを選択して、サービス レベルを設定または変更します。 
- **[DTU]** スライダーを使用して、サービス レベル内のパフォーマンス レベル (**DTU**) を設定または変更します。
- **[ストレージ]** スライダーを使用して、パフォーマンス レベルのストレージ容量を設定または変更します。 

![サービス レベルとパフォーマンス レベルの構成](./media/sql-database-single-database-resources/change-service-tier.png)

**[概要]** をクリックして、実行中の操作の監視や取り消しを行います。

![操作を取り消す](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> P11 または P15 サービス レベルを選択する場合は、「[最大サイズ 4 TB の P11 および P15 データベースの現時点での制限事項](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)」をご覧ください。
>

## <a name="manage-single-database-resources-using-powershell"></a>PowerShell を使って単一データベース リソースを管理する

PowerShell を使って、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の PowerShell コマンドレットを使います。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

| コマンドレット | [説明] |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|データベースを作成します。 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|1 つまたは複数のデータベースを取得します。|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|データベースのプロパティを設定するか、既存のデータベースをエラスティック プールに移動します。 たとえば、データベースの最大サイズを設定するには、**MaxSizeBytes** プロパティを使います。|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|データベース操作の状態を取得します。 |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|データベースに対する非同期更新操作を取り消します。|


> [!TIP]
> データベースのパフォーマンス メトリックを監視し、そのデータベースを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成する PowerShell のサンプル スクリプトについては、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)」を参照してください。

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Azure CLI を使って単一データベース リソースを管理する

Azure CLI を使って、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の [Azure CLI SQL Database](/cli/azure/sql/db) コマンドを使います。 [Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。 SQL エラスティック プールの作成と管理については、[エラスティック プール](sql-database-elastic-pool.md)に関する記事を参照してください。

| コマンドレット | [説明] |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|サーバーのファイアウォール規則を作成します。|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|サーバーのファイアウォール規則を一覧表示します。|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|ファイアウォール規則の詳細を表示します。|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|ファイアウォール規則を更新します。|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|ファイアウォール規則を削除します。|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|データベースに対して実行された操作の一覧を取得します。|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|データベースに対する非同期操作を取り消します。|

> [!TIP]
> 単一の Azure SQL データベースのサイズ情報を照会した後に、そのデータベースを別のパフォーマンス レベルにスケーリングする Azure CLI のサンプル スクリプトについては、「[CLI を使用して 1 つの SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-cli.md)」を参照してください。
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Transact-SQL を使って単一データベース リソースを管理する

Transact-SQL を使って、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の T-SQL コマンドを使います。 これらのコマンドは、Azure Portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)、または Azure SQL Database サーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。 

| コマンド | [説明] |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|新しいデータベースを作成します。 新しいデータベースを作成するには、マスター データベースに接続する必要があります。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL データベースを変更します。 |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database または Azure SQL Data Warehouse のエディション (サービス レベル)、サービス目標 (価格レベル)、およびエラスティック プール名 (存在する場合) を返します。 Azure SQL Database サーバーの master データベースにログオンしている場合は、すべてのデータベースの情報が返されます。 Azure SQL Data Warehouse の場合は、master データベースに接続する必要があります。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Azure SQL Database サーバー上のデータベースの数、種類、および期間を一覧表示します。|

次の例では、ALTER DATABASE コマンドを使ってデータベースの最大サイズを変更する方法を示します。

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>REST API を使って単一データベース リソースを管理する

Azure SQL Database のサービス レベル、パフォーマンス レベル、ストレージ量を設定または変更するには、次の REST API 要求を使います。

| コマンド | [説明] |
| --- | --- |
|[データベース - 作成または更新](/rest/api/sql/databases/createorupdate)|新しいデータベースを作成するか、既存のデータベースを更新します。|
|[データベース - 取得](/rest/api/sql/databases/get)|データベースを取得します。|
|[データベース - エラスティック プールごとに取得](/rest/api/sql/databases/getbyelasticpool)|エラスティック プール内のデータベースを取得します。|
|[データベース - 推奨されるエラスティック プールごとに取得](/rest/api/sql/databases/getbyrecommendedelasticpool)|推奨されるエラスティック プール内のデータベースを取得します。|
|[データベース - エラスティック プールごとの一覧取得](/rest/api/sql/databases/listbyelasticpool)|エラスティック プール内のデータベースの一覧を返します。|
|[データベース - 推奨されるエラスティック プールごとの一覧取得](/rest/api/sql/databases/listbyrecommendedelasticpool)|推奨されるエラスティック プール内のデータベースの一覧を返します。|
|[データベース - サーバーごとの一覧取得](/rest/api/sql/databases/listbyserver)|サーバー内のデータベースの一覧を返します。|
|[データベース - 更新](/rest/api/sql/databases/update)|既存のデータベースを更新します。|
|[Operations - List](/rest/api/sql/Operations/List)|利用可能な SQL Rest API 操作をすべて表示します。|



## <a name="next-steps"></a>次の手順

- サービス レベル、パフォーマンス レベル、およびストレージ量については、「[Azure SQL Database で利用可能なパフォーマンス オプション](sql-database-service-tiers.md)」をご覧ください。
- エラスティック プールについては、「[エラスティック プールを利用して複数の SQL データベースの管理およびスケーリングを行う](sql-database-elastic-pool.md)」をご覧ください。
- 「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
