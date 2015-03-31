<properties 
   pageTitle="REST API で Geo-Restore を使用して Azure SQL データベースを回復する" 
   description="Geo-Restore, Microsoft Azure SQL データベース. データベースの復元、データベースの回復, REST API" 
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

# REST API で Geo-Restore を使用して Azure SQL データベースを回復する

> [AZURE.SELECTOR]
- [Geo-Restore - ポータル](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Geo-Restore - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-powershell/)

## 概要

このガイドでは REST API を使用して Azure SQL データベースを回復する方法を説明します。さらに詳しい操作へのリンクが提供されます。Geo-Restore は、すべての Azure SQL データベース サービス レベル (Basic、Standard、Premium) に含まれる主要な災害回復保護です。

## 制限事項とセキュリティ

「[Azure ポータルで Geo-Restore を使用して Azure SQL データベースを回復する](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)」をご覧ください。

## 方法:REST API を使用して Azure SQL データベースを回復する

1.	[List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx) 操作で回復可能なデータベースの一覧を取得します。
	
2.	[Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx) 操作で回復するデータベースを取得します。
	
3.	[Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx) 操作を使用して回復要求を作成します。
	
4.	[Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) 操作で回復の状態を追跡します。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure ポータルでポイント イン タイム リストアを使用して Azure SQL データベースを復元する](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Azure ポータルで削除した Azure SQL データベースを復元する](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL データベース Geo-Restore (ブログ)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[サービス管理 REST API のリファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
