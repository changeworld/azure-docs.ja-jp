<properties 
   pageTitle="SQL Database のユーザー エラーの復旧" 
   description="Azure SQL Database の特定時点に復元 (PITR) 機能を使用して、ユーザー エラー、偶発的なデータの破損、または削除済みデータベースを復旧する方法について説明します。" 
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
   ms.date="02/09/2016"
   ms.author="elfish"/>

# ユーザー エラーからの Azure SQL Database の復旧

Azure SQL Database は、ユーザー エラーまたは予期しないデータ変更から復旧するための、2 つの中核的な機能を提供します。

- ポイントインタイム リストア 
- 削除済みデータベースの復元

この[ブログの投稿](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)では、これらの機能の詳細について説明します。

Azure SQL Database では、常に新しいデータベースに復元します。これらの復元機能は、Basic、Standard、Premium のすべてのデータベースに提供されます。

##ポイントインタイム リストア
ユーザー エラーや意図しないデータ変更が発生した場合、ポイントインタイム リストア (PITR) 機能を使用して、データベースの保持期間内の任意の時点にデータベースを復元できます。

Basic データベースの保持期間は 7 日間、Standard データベースの保持期間は 14 日間、Premium データベースの保持期間は 35 日です。データベースの保持期間の詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。

> [AZURE.NOTE] データベースを復元すると、新しいデータベースが作成されます。復元先サーバーに新しいデータベース用の十分な DTU 容量があることが重要です。このクォータの引き上げをご希望の場合は、[サポートに連絡](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)してください。

###Azure ポータル
Azure ポータルでポイントインタイム リストアを使用するには、次の手順に従います。

1. [Azure ポータル](https://portal.Azure.com)にログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL データベース]** を選択します。
3. 目的のデータベースに移動し、それを選択します。
4. データベースのブレードの上部で **[復元]** を選択します。
5. データベース名と特定の時点を指定して **[作成]** をクリックします。
6. データベースの復元処理が開始され、画面の左側にある **[通知]** を使用してこれを監視することができます。

###PowerShell
プログラムでポイントインタイム リストアを実行するには、PowerShell の [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) コマンドレットを使用します。詳細については、[この手順に関するビデオをご覧ください](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/)。

> [AZURE.IMPORTANT] この記事には、バージョン 1.0 *より前*の Azure PowerShell に対応するコマンドが含まれています。Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST API 
プログラムでデータベースの復元を実行するには、REST を使用します。

1. 「[データベースの取得](http://msdn.microsoft.com/library/azure/dn505708.aspx)」の操作を使用して、復元するデータベースを取得します。

2.	「[データベースの復元要求の作成](http://msdn.microsoft.com/library/azure/dn509571.aspx)」の操作を使用して、復元要求を作成します。
	
3.	「[データベース操作の状態](http://msdn.microsoft.com/library/azure/dn720371.aspx)」の操作を使用して、復元要求を追跡します。

##削除されたデータベースの復元
データベースが削除された場合、Azure SQL Database では、削除されたデータベースを削除された時点の状態に復元できます。Azure SQL Database では、データベースの保持期間にわたり、削除されたデータベースのバックアップを格納します。

削除されたデータベースの保持期間は、データベースのサービス階層に基づく日数とデータベースが存在していた日数のどちらか短い方になります。データベースの保持期間の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをお読みください。

> [AZURE.NOTE] データベースを復元すると、新しいデータベースが作成されます。復元先サーバーに新しいデータベース用の十分な DTU 容量があることが重要です。このクォータの引き上げをご希望の場合は、[サポートに連絡](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)してください。

###Azure ポータル
削除されたデータベースを Azure ポータルを使用して復元するには、次の手順に従います。

1. [Azure ポータル](https://portal.Azure.com)にログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します。
3. 目的のサーバーに移動し、それを選択します。
4. 下へスクロールしてサーバーのブレードの **[操作]** を表示し、**[削除済みデータベース]** タイルをクリックします。
5. 復元する削除済みデータベースを選択します。
6. データベースの名前を指定し、**[作成]** をクリックします。
7. データベースの復元処理が開始され、画面の左側にある **[通知]** を使用してこれを監視することができます。

###PowerShell
削除されたデータベースを PowerShell を使用して復元するには、[Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) コマンドレットを使用します。詳細については、[この手順に関するビデオをご覧ください](https://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/)。

1. 削除済みデータベースと削除日は、削除済みデータベースの一覧から検索します。
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. 削除された特定のデータベースを取得し、復元を開始します。

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST API 
プログラムでデータベースの復元を実行するには、REST を使用します。

1.	「[削除された復元可能なデータベースの一覧表示](http://msdn.microsoft.com/library/azure/dn509562.aspx)」の操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
	
2.	「[削除された復元可能なデータベースの取得](http://msdn.microsoft.com/library/azure/dn509574.aspx)」の操作を使用して、復元する削除済みデータベースの詳細を取得します。

3.	「[データベースの復元要求の作成](http://msdn.microsoft.com/library/azure/dn509571.aspx)」の操作を使用して、復元を開始します。
	
4.	「[データベース操作の状態](http://msdn.microsoft.com/library/azure/dn720371.aspx)」の操作を使用して、復元の状態を追跡します。

<!---HONumber=AcomDC_0211_2016-->