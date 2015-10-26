<properties 
   pageTitle="Azure PowerShell でポイントインタイム リストアを使用して Azure SQL データベースを復元する" 
   description="ポイントインタイム リストア, Microsoft Azure SQL Database, データベースの復元, データベースの回復, Azure PowerShell" 
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

# Azure PowerShell でポイントインタイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## 概要

このチュートリアルでは、[Azure PowerShell](../powershell-install-configure.md) でポイントインタイム リストアを使用して Azure SQL データベースを復元する方法について説明します。Azure SQL Database には Basic、Standard、Premium サービス レベルでポイントインタイム リストアのセルフ サービスをサポートするバックアップが組み込まれています。

ポイントインタイム リストアでは新しいデータベースを作成します。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルでポイントインタイム リストアを使用して Azure SQL データベースを復元する](sql-database-point-in-time-restore-tutorial-management-portal.md)」を参照してください。

## 方法: Azure PowerShell でポイントインタイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell]

次のコマンドレットを実行するには証明書ベースの認証を使用する必要があります。詳細については、「[Azure PowerShell のインストールと構成方法](../powershell-install-configure.md#use-the-certificate-method)」の「*証明書メソッドを使用する*」セクションを参照してください。

> [AZURE.IMPORTANT]この記事には、バージョン 1.0 *未満*の Azure PowerShell に対応するコマンドが含まれています。Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) コマンドレットを使用して復元するデータベースを取得します。次のパラメーターを指定します。
	* データベースが存在する場所の **ServerName**。
	* 復元するデータベースの **DatabaseName**。	

	`$Database = Get-AzureSqlDatabase -ServerName "myserver" –DatabaseName “mydb”`

2. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) コマンドレットを使用して復元を開始します。次のパラメーターを指定します。
	* 復元する **SourceDatabase**。
	* 復元先のデータベースの **TargetDatabaseName**。
	* 復元する **PointInTime**。

	**$RestoreRequest** という変数に返された結果を格納します。この変数には、復元の状態を監視するための復元要求 ID が含まれています。

	`$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “myrestoredDB” –PointInTime “2015-01-01 06:00:00”`

復元が完了するまで時間がかかる場合があります。復元の状態を監視するには、[Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) コマンドレットを使用して、次のパラメーターを指定します。

* 復元先のデータベースの **ServerName**。
* 手順 2 で **$RestoreRequest** 変数に格納した復元要求 ID **OperationGuid**。

	`Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

**[State]** と **[PercentComplete]** のフィールドには復元の状態が表示されます。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL Database のビジネス継続性](sql-database-business-continuity.md)

[Azure SQL Database のポイントインタイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=Oct15_HO3-->