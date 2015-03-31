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
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# REST API でポイント イン タイム リストアを使用して Azure SQL データベースを復元する

> [AZURE.SELECTOR]
- [ポイント イン タイム リストア - ポータル](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [ポイントイン タイム リストア - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)

## 概要

このガイドでは、REST API でポイント イン タイム リストアを使用して Azure SQL データベースを復元する方法を説明します。さらに詳しい操作へのリンクが提供されます。

ポイント イン タイム リストアでは新しいデータベースを作成します。サービスでは、復元ポイントに使用されるバックアップを基にサービス レベルを自動的にを選択します。別のデータベースを作成するためには、論理サーバーに利用可能なクォータがあることを確認します。クォータの増加を要請する場合は、[Azure サポート](http://azure.microsoft.com/support/options/) までお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)」をご覧ください。

## 方法:REST API を使用して Azure SQL データベースを復元する

1.	[Get Database](http://msdn.microsoft.com/library/azure/dn505708.aspx) 操作で復元するデータベースを取得します。

2.	[Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx) 操作で復元要求を作成します。
	
3.	[Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) 操作で復元要求を追跡します。

## 次のステップ

詳細については、次のトピックを参照してください。 

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベースのポイント イン タイム リストア (ブログ)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[サービス管理 REST API のリファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
