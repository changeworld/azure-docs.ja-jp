<properties 
   pageTitle="Azure PowerShell で Geo-Restore を使用して Azure SQL データベースを回復する" 
   description="Geo-Restore, Microsoft Azure SQL Database, データベースの復元., データベースの回復, Azure PowerShell" 
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
   ms.author="elfish; v-romcal; sstein"/>

# Azure PowerShell で Geo-Restore を使用して Azure SQL データベースを回復する

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## 概要

このチュートリアルでは、[Azure PowerShell](../powershell-install-configure.md) で Geo-Restore を使用して Azure SQL データベースを回復する方法について説明します。Geo-Restore は、すべての Azure SQL データベース サービス レベル (Basic、Standard、Premium) に含まれる主要な障害復旧保護です。

> [AZURE.IMPORTANT]この記事には、バージョン 1.0 *未満*の Azure PowerShell に対応するコマンドが含まれています。Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。

## 制限事項とセキュリティ

「[Azure ポータルで Geo-Restore を使用して Azure SQL データベースを回復する](sql-database-geo-restore-tutorial-management-portal.md)」を参照してください。

## 方法: Azure PowerShell で Geo-Restore を使用して Azure SQL データベースを回復する

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell]

次のコマンドレットを実行するには証明書ベースの認証を使用する必要があります。詳細については、「[Azure PowerShell のインストールと構成方法](../powershell-install-configure.md#use-the-certificate-method)」の「*証明書メソッドを使用する*」セクションを参照してください。

1. [Get-azuresqlrecoverabledatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) コマンドレットを使用して回復可能なデータベースの一覧を取得します。次のパラメーターを指定します。
	* データベースが存在する場所の **ServerName**。	

	`Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. [Get-azuresqlrecoverabledatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) コマンドレットを使用して、回復するデータベースを選択します。次のパラメーターを指定します。
	* データベースが存在する場所の **ServerName**。
	* 回復するデータベースの **DatabaseName**。

	`$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" –DatabaseName “mydb”`
	 
3. [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx) コマンドレットを使用して回復を開始します。次のパラメーターを指定します。
	* 回復する **SourceDatabase**。
	* 回復先のデータベースの **TargetDatabaseName**。
	* データベース回復先の **TargetServerName**。

	**$RestoreRequest** という変数に返された結果を格納します。この変数には、復元の状態を監視するための復元要求 ID が含まれています。

	`$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “myrecoveredDB” –TargetServerName “mytargetserver”`
	
データベースの回復は完了するまで時間がかかる場合があります。回復の状態を監視するには、[Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) コマンドレットを使用して、次のパラメーターを指定します。

* 復元先のデータベースの **ServerName**。
* 手順 3 で **$RecoveryRequest** 変数に格納した復元要求 ID **OperationGuid**。

	`Get-AzureSqlDatabaseOperation –ServerName “mytargetserver” –OperationGuid $RecoveryRequest.ID`

**[State]** と **[PercentComplete]** のフィールドには復元の状態が表示されます。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure ポータルでポイントインタイム リストアを使用して Azure SQL データベースを復元する](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Azure ポータルで削除した Azure SQL データベースを復元する](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Azure SQL Database のビジネス継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database の geo リストア (ブログ)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=Oct15_HO3-->