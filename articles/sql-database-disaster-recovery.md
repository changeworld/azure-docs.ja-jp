<properties 
   pageTitle="SQL Database の災害復旧" 
   description="Azure SQL Database の geo レプリケーションと地理リストア機能を使用して、地域のデータ センターの停止や障害からデータベースを復旧する方法について説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Azure SQL Database を障害から回復する

Azure SQL Database は、障害復旧機能をいくつか提供しています。

- アクティブ geo レプリケーション [(ブログ)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- 標準 geo レプリケーション [(ブログ)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- 地理リストア [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

災害とデータベースを復旧するための準備の詳細については、「[ビジネス継続性のための設計](sql-database-business-continuity-design.md)」をご覧ください。

##復旧を開始するタイミング 

復旧操作はアプリケーションに影響します。SQL 接続文字列を変更する必要があり、永続的にデータが失われる可能性があります。そのため、障害がアプリケーションの RTO よりも長くかかる可能性が高い場合にのみ行ってください。アプリケーションが実稼働環境にデプロイされている場合は、アプリケーションの健全性の定期的な監視を実行し、次のデータ ポイントを使用して、復旧が保証されていることをアサートします。

1. 接続の永続的な障害は、アプリケーション層からデータベースに渡ります。
2. Azure ポータルは、幅広い影響のある地域でのインシデントに関するアラートを示します。

## Geo レプリケートのセカンダリ データベースへのフェールオーバー
> [AZURE.NOTE][標準 geo レプリケーション](https://msdn.microsoft.com/library/azure/dn758204.aspx)か[アクティブ geo レプリケーション](https://msdn.microsoft.com/library/azure/dn741339.aspx)を構成し、フェールオーバーに使用するセカンダリ データベースを準備する必要があります。geo レプリケーションでは、標準データベースとプレミアム データベースにのみ使用できます。

プライマリ データベースで障害が発生した場合は、セカンダリ データベースにフェールオーバーして可用性を復元することができます。これを行うには、連続コピー リレーションシップを強制的に終了させる必要があります。連続コピー リレーションシップの終了の詳細については、[ここ](https://msdn.microsoft.com/library/azure/dn741323.aspx)をご確認ください。



###Azure ポータル
1. [Azure ポータル](https://portal.Azure.com)にログインします。
2. 画面の左側にある、**[参照]** をクリックして、**[SQL Database]** を選択します。
3. 目的のデータベースに移動し、それを選択します。 
4. データベースのブレードの下部にある **[Geo Replication map]** を選択します。
4. **[セカンダリ]** の下で、復旧するデータベースの名前の行を右クリックし、**[停止]** を選択します。

連続コピー リレーションシップの終了後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。
###PowerShell
PowerShell を使用して、プログラムでデータベースの復旧を実行します。

セカンダリ データベースからリレーションシップを終了するには、[Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223) コマンドレットを使用します。
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
連続コピー リレーションシップの終了後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。
###REST API 
REST を使用して、プログラムでデータベースの復旧を実行します。

1. 「[データベース コピーの取得](https://msdn.microsoft.com/library/azure/dn509570.aspx)」の操作を使用してデータベースの連続コピーを取得します。
2. 「[データベース コピーの停止](https://msdn.microsoft.com/library/azure/dn509573.aspx)」の操作を使用してデータベースの連続コピーを停止します。データベース コピーの停止要求 URI のセカンダリ サーバー名とデータベース名を使用します。

 連続コピー リレーションシップの終了後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。
## 地理リストアを使用した復旧

データベースの障害が発生した場合は、地理リストアを使用して、最新の地理冗長バックアップからデータベースを復旧できます。

###Azure ポータル
1. [Azure ポータル](https://portal.Azure.com)にログインします。
2. 画面の左側にある、**[新規]** を選択し、次に **[データとストレージ]** を選択し、次に **[SQL Database]** を選択します。
2. ソースとして **[バックアップ]** を選択し、復旧する地理冗長バックアップを選択します。
3. データベースのプロパティの残りの部分を指定して **[作成]** をクリックします。
4. データベースの復元処理が開始され、画面の左側にある **[通知]** を使用してこれを監視することができます。

データベースの復旧後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](articles/sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。
###PowerShell 
PowerShell を使用して、プログラムでデータベースの復旧を実行します。

地理リストア要求を開始するには、[start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx) を使用します。手順の詳細については、「[ハウツー ビデオ](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/)」をご覧ください。

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

データベースの復旧後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。
###REST API 

REST を使用して、プログラムでデータベースの復旧を実行します。

1.	[List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx) 操作で回復可能なデータベースの一覧を取得します。
	
2.	[Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx) 操作で回復するデータベースを取得します。
	
3.	[Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx) 操作を使用して回復要求を作成します。
	
4.	[Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) 操作で回復の状態を追跡します。

データベースの復旧後に、「[Finalize a Recovered Database (復旧データベースの最終処理)](sql-database-recovered-finalize.md)」ガイドに従って、復旧したデータベースを構成することができます。

<!---HONumber=58-->