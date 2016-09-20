<properties 
    pageTitle="PowerShell を使用した Azure SQL Database のコピー | Microsoft Azure" 
    description="PowerShell を使用した Azure SQL Database のコピーの作成" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用した Azure SQL Database のコピー


> [AZURE.SELECTOR]
- [概要](sql-database-copy.md)
- [Azure ポータル](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

次の手順では、PowerShell を使用して、同じサーバーまたは別のサーバーに SQL データベースをコピーする方法を説明します。このデータベースのコピー操作では、[Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) コマンドレットを利用します。


この記事を完了するには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料評価版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- Azure PowerShell。Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。



## SQL データベースのコピー

変数には、例の値を、使用するデータベースとサーバーの特定の値に置き換える必要があるものがいくつかあります。プレースホルダー値をお使いの環境の値で置き換えます。

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### SQL データベースを同じサーバーにコピーする

このコマンドでは、サービスにデータベースのコピー要求を送信します。データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

Azure Resource Manager コマンドレットの使用:

    # Copy a database to the same server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyDatabaseName $PartnerDatabaseName

### 別のサーバーへの SQL データベースのコピー

このコマンドでは、サービスにデータベースのコピー要求を送信します。データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
Azure Resource Manager コマンドレットの使用:

    # Copy a database to a different server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyServerName $PartnerServerName -CopyDatabaseName $PartnerDatabaseName

## コピー操作の進行状況の監視

**Start-AzureSqlDatabaseCopy** の実行後に、コピー要求の状態を確認できます。要求直後にこれを実行すると、通常は、**State : Pending** または **State : Running** が返されます。したがって、出力に **State : COMPLETED** が表示されるまで、これを複数回実行できます。


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## ログインの解決

コピー操作が完了した後にログインを解決するには、[ログインの解決](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)に関するページをご覧ください


## PowerShell サンプル スクリプト

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 次のステップ

- Azure SQL Database のコピーの概要については、「[Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。
- Azure ポータルを使用してデータベースをコピーするには、「[Copy an Azure SQL database using the Azure Portal (Azure ポータルを使用した Azure SQL Database のコピー)](sql-database-copy-portal.md)」をご覧ください。
- Transact-SQL を使用してデータベースをコピーするには、「[T-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md)」をご覧ください。
- 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。


## その他のリソース

- [ログインの管理](sql-database-manage-logins.md)
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
- [データベースを BACPAC にエクスポートする](sql-database-export.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0907_2016-->