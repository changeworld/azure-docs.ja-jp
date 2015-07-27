<properties 
   pageTitle="REST API でポイント イン タイム リストアを使用して Azure SQL データベースを復元する" 
   description="ポイント イン タイム リストア, Microsoft Azure SQL データベース, データベースの復元, データベースの回復, REST API" 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# REST API でポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## 概要

このガイドでは、REST API でポイント イン タイム リストアを使用して Azure SQL データベースを復元する方法を説明します。詳細な操作方法については、リンク先をご覧ください。

ポイント イン タイム リストアでは新しいデータベースを作成します。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する](sql-database-point-in-time-restore-tutorial-management-portal.md)」を参照してください。

## 方法: REST API を使用して Azure SQL データベースを復元する

1.	「[データベースの取得](http://msdn.microsoft.com/library/azure/dn505708.aspx)」の操作を使用して、復元するデータベースを取得します。

2.	「[データベースの復元要求の作成](http://msdn.microsoft.com/library/azure/dn509571.aspx)」の操作を使用して、復元要求を作成します。
	
3.	「[データベース操作の状態](http://msdn.microsoft.com/library/azure/dn720371.aspx)」の操作を使用して、復元要求を追跡します。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベースのポイント イン タイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[サービス管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=July15_HO3-->