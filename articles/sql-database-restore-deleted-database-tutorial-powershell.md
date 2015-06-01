<properties 
   pageTitle="Azure PowerShell で削除した Azure SQL データベースを復元する" 
   description="Microsoft Azure SQL データベース, 削除したデータベースの復元, 削除したデータベースの回復, Azure PowerShell" 
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

# Azure PowerShell で削除した Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [削除したデータベースを復元する - ポータル](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [削除したデータベースを復元する - REST API](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## 概要

このチュートリアルでは、[Azure  PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) で削除した Azure SQL データベースを復元する方法について説明します。保有期間中に削除したデータベースを削除した時点に復元ができます。保有期間は、データベースのサービス レベルによって決まります。

削除した Azure SQL データベースを復元すると新しいデータベースが作成されます。サービスでは、復元ポイントに使用されるバックアップを基にサービス レベルを自動的にを選択します。別のデータベースを作成するためには、論理サーバーに利用可能なクォータがあることを確認します。クォータの増加を要請する場合は、[Azure サポート](http://azure.microsoft.com/support/options/) までお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルで削除した Azure SQL データベースを復元する](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)」をご覧ください。

## 方法:Azure PowerShell で削除した Azure SQL データベースを復元する

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

次のコマンドレットを実行するには証明書ベースの認証を使用する必要があります。詳しくは、「[Azure PowerShell のインストールと構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method)」の  *Use the certificate method (証明書メソッドを使用する)* セクションをご覧ください。

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) コマンドレットを使用して回復可能なデータベースの一覧を取得します。
	* **RestorableDropped** スイッチで、データベースを削除したサーバーの **ServerName** を指定します。
	* 次のコマンドを実行すると結果が **$RecoverableDBs** という名前の変数に格納されます。
	
	`PS C:>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" -RestorableDropped`

2. 削除済みデータベースの一覧から復元するデータベースを選びます。

	* **$RecoverableDBs** の一覧から削除したデータベースの番号を入力します。  

	`PS C:>$Database = $RecoverableDBs[<deleted database number>]`

	* 復元可能な削除済みデータベース オブジェクトの取得方法について詳しくは、 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx) をご覧ください。

3. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) コマンドレットを使用して復元を開始します。次のパラメーターを指定します。	
	* **SourceRestorableDroppedDatabase**
	* 復元先のデータベースの **TargetDatabaseName**。

	**$RestoreRequest** と呼ばれる変数に返された結果を格納します。この変数には、復元の状態を監視するための復元要求 ID が含まれています。
	
	`PS C:>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database -TargetDatabaseName "myrestoredDB"`

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、[Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) コマンドレットを使用して、次のパラメーターを指定します。

* 復元先のデータベースの **ServerName**。
* 手順 3 で **$RestoreRequest** 変数に格納した復元要求 ID **OperationGuid**。

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

**[State (状態)]** と **[PercentComplete]** フィールドでは復元の状態を表示します。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)
<!--HONumber=47-->
