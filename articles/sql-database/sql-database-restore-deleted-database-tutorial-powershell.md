<properties 
   pageTitle="Azure PowerShell で削除した Azure SQL データベースを復元する" 
   description="Microsoft Azure SQL Database, 削除したデータベースの復元, 削除したデータベースの回復, Azure PowerShell" 
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
   ms.date="10/08/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Azure PowerShell で削除した Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## 概要

このチュートリアルでは、[Azure PowerShell](../powershell-install-configure.md) で削除した Azure SQL データベースを復元する方法について説明します。保有期間中に削除したデータベースを削除した時点に復元ができます。保有期間は、データベースのサービス レベルによって決まります。

削除済みの Azure SQL データベースを復元すると新しいデータベースが作成されます。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルで削除された Azure SQL データベースの復元](sql-database-restore-deleted-database-tutorial-management-portal.md)」を参照してください。

## 方法: Azure PowerShell で削除した Azure SQL データベースを復元する

> [AZURE.VIDEO restore-a-deleted-sql-database-with-microsoft-azure-powershell]

次のコマンドレットを実行するには証明書ベースの認証を使用する必要があります。詳細については、「[Azure PowerShell のインストールと構成方法](../powershell-install-configure.md#use-the-certificate-method)」の「*証明書メソッドを使用する*」セクションを参照してください。

> [AZURE.IMPORTANT]この記事には、バージョン 1.0 *未満*の Azure PowerShell に対応するコマンドが含まれています。Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) コマンドレットを使用して回復可能なデータベースの一覧を取得します。
	* **RestorableDropped** スイッチを使用して、データベースを削除したサーバーの **ServerName** を指定します。
	* 次のコマンドを実行すると結果が **$RecoverableDBs** という変数に格納されます。
	
	`$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" –RestorableDropped`

2. 削除済みデータベースの一覧から復元するデータベースを選びます。

	* **$RecoverableDBs** の一覧から削除したデータベースの番号を入力します。  

	`$Database = $RecoverableDBs[<deleted database number>]`

	* 復元可能な削除済みデータベース オブジェクトの取得方法について詳しくは、「[Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx)」を参照してください。

3. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) コマンドレットを使用して復元を開始します。次のパラメーターを指定します。
	* **SourceRestorableDroppedDatabase**
	* 復元先のデータベースの **TargetDatabaseName**。

	**$RestoreRequest** という変数に返された結果を格納します。この変数には、復元の状態を監視するための復元要求 ID が含まれています。
	
	`$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “myrestoredDB”`

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、[Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) コマンドレットを使用して、次のパラメーターを指定します。

* 復元先のデータベースの **ServerName**。
* 手順 3 で **$RestoreRequest** 変数に格納した復元要求 ID **OperationGuid**。

	`Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

**[State]** と **[PercentComplete]** のフィールドには復元の状態が表示されます。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL Database のビジネス継続性](sql-database-business-continuity.md)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)

<!---HONumber=Oct15_HO3-->