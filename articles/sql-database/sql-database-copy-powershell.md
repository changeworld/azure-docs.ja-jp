---
title: PowerShell を使用した Azure SQL Database のコピー | Microsoft Docs
description: PowerShell を使用した Azure SQL Database のコピーの作成
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# PowerShell を使用した Azure SQL Database のコピー
> [!div class="op_single_selector"]
> * [概要](sql-database-copy.md)
> * [Azure ポータル](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

この記事では、PowerShell を使用して、SQL データベースを同じサーバーおよび別のサーバーにコピーする方法、またはデータベースを[エラスティック データベース プール](sql-database-elastic-pool.md)にコピーする方法について説明します。このデータベースのコピー操作では、[New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) コマンドレットを使用します。

この記事を完了するには、以下が必要です。

* Azure SQL データベース (コピーするデータベース)。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
* Azure PowerShell の最新バージョン。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。

SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../resource-group-overview.md)を使用している場合にのみサポートされます。したがって、例では、Resource Manager の [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)を使用しています。既存のクラシック デプロイ モデルの [Azure SQL Database (クラシック) コマンドレット](https://msdn.microsoft.com/library/azure/dn546723.aspx)は下位互換性を確保するためにサポートされていますが、Resource Manager のコマンドレットを使用することをお勧めします。

> [!NOTE]
> データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。
> 
> 

## SQL データベースを同じサーバーにコピーする
同じサーバーにコピーを作成するには、`-CopyServerName` パラメーターを省略します (または、そのパラメーターを同じサーバーに設定します)。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## 別のサーバーへの SQL データベースのコピー
別のサーバーにコピーを作成するには、`-CopyServerName` パラメーターを追加して、そのパラメーターを別のサーバーに設定します。"*コピー*" サーバーは既に存在している必要があります。このサーバーが異なるリソース グループに含まれる場合は、`-CopyResourceGroupName` パラメーターを指定する必要があります。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## エラスティック データベース プールへの SQL データベースのコピー
SQL データベースのコピーをプールに作成するには、`-ElasticPoolName` パラメーターを既存のプールに設定します。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## ログインの解決
コピー操作が完了した後にログインを解決するには、[ログインの解決](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)に関するページをご覧ください

## PowerShell サンプル スクリプト
次のスクリプトでは、すべてのリソース グループ、サーバー、およびプールが既に存在するものとしています (変数の値は、既存のリソースに置き換えてください)。データベース コピーを除き、すべてが存在している必要があります。

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## 次のステップ
* Azure SQL Database のコピーの概要については、「[Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。
* Azure ポータルを使用してデータベースをコピーするには、「[Azure ポータルを使用した Azure SQL Database のコピー](sql-database-copy-portal.md)」を参照してください。
* Transact-SQL を使用してデータベースをコピーするには、「[T-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md)」をご覧ください。
* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。

## その他のリソース
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
* [ログインの管理](sql-database-manage-logins.md)
* [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
* [データベースを BACPAC にエクスポートする](sql-database-export.md)
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database の PowerShell コマンドレット リファレンス](https://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0914_2016-->