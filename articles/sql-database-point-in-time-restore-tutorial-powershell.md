<properties 
   pageTitle="Azure PowerShell でポイント イン タイム リストアを使用して Azure SQL データベースを復元する" 
   description="ポイント イン タイム リストア, Microsoft Azure SQL データベース, データベースの復元, データベースの回復, Azure  PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Azure PowerShell でポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [ポイント イン タイム リストア - ポータル](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [ポイント イン タイム リストア - REST API](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## 概要

このチュートリアルでは、[Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) でポイント イン タイム リストアを使用して Azure SQL データベースを復元する方法について説明します。Azure SQL データベースには Basic、Standard、Premium サービス レベルでポイント イン タイム リストアのセルフ サービスをサポートするバックアップが組み込まれています。

ポイント イン タイム リストアでは新しいデータベースを作成します。サービスでは、復元ポイントに使用されるバックアップを基にサービス レベルを自動的にを選択します。別のデータベースを作成するためには、論理サーバーに利用可能なクォータがあることを確認します。クォータの増加を要請する場合は、[Azure サポート](http://azure.microsoft.com/support/options/) までお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)」をご覧ください。

## 方法:Azure PowerShell でポイント イン タイム リストアを使用して Azure SQL データベースを復元する

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

次のコマンドレットを実行するには証明書ベースの認証を使用する必要があります。詳しくは、「[Azure PowerShell のインストールと構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method)」 の  *Use the certificate method* セクションをご覧ください。

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) コマンドレットを使用して復元するデータベースを取得します。次のパラメーターを指定します。
	* データベースが存在する場所の **ServerName**。
	* 復元するデータベースの **DatabaseName**。	

	`PS C:>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) コマンドレットを使用して復元を開始します。次のパラメーターを指定します。	
	* 復元する **SourceDatabase**。
	* 復元先のデータベースの **TargetDatabaseName**。
	* 復元する **PointInTime**。

	**$RestoreRequest** と呼ばれる変数に返された結果を格納します。この変数には、復元の状態を監視するための復元要求 ID が含まれています。 

	`PS C:>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、[Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) コマンドレットを使用して、次のパラメーターを指定します。

* 復元先のデータベースの **ServerName**。
* 手順 2 で **$RestoreRequest** 変数に格納した復元要求 ID **OperationGuid**。

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

**[State (状態)]** と **[PercentComplete]** フィールドでは復元の状態を表示します。 

## 次のステップ

詳細については、次のトピックを参照してください。  

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベースのポイント イン タイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
