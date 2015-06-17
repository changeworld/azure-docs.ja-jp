<properties 
   pageTitle="削除済み Azure SQL データベースの REST API による復元" 
   description="Microsoft Azure SQL データベース, 削除済みのデータベースの復元, 削除済みのデータベースのリカバリ, REST API" 
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

# 削除済み Azure SQL データベースの REST API による復元

> [AZURE.SELECTOR]
- [削除済みデータベースの復元 - ポータル](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [削除済みデータベースの復元 - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-powershell/)

## 概要

このガイドでは、削除済みの Azure SQL データベースを REST API を使用して復元する方法を説明します。詳細な操作方法については、リンク先をご覧ください。

削除済みの Azure SQL データベースを復元すると新しいデータベースが作成されます。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

「[Restore a deleted Azure SQL database in the Azure portal (Azure ポータルで削除された Azure SQL データベースの復元)](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)」をご覧ください。

## 方法:削除済みの Azure SQL データベースを REST API を使用して復元する

1.	[List Restorable Dropped Databases](http://msdn.microsoft.com/library/azure/dn509562.aspx) 操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
	
2.	[Get Restorable Dropped Database](http://msdn.microsoft.com/library/azure/dn509574.aspx) 操作を使用して、復元する削除済みデータベースの詳細を取得します。

3.	[Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx) 操作を使用して、復元を開始します。
	
4.	[Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) 操作を使用して、復元の状態を追跡します。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[サービス管理 REST API リファレンス](http://msdn.microsoft.com/library/azure/ee460799.aspx)
<!--HONumber=47-->
 