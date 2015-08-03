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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# 削除済み Azure SQL データベースの REST API による復元

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## 概要

このガイドでは、削除済みの Azure SQL データベースを REST API を使用して復元する方法を説明します。詳細な操作方法については、リンク先をご覧ください。

削除済みの Azure SQL データベースを復元すると新しいデータベースが作成されます。サービスは、復元ポイントに使用したバックアップに基づいて、サービス層を自動的に選択します。論理サーバーに別のデータベースを作成するための利用可能なクォータがあることを確認します。クォータを増やす場合は、[Azure サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

## 制限事項とセキュリティ

「[Azure ポータルで削除された Azure SQL データベースの復元](sql-database-restore-deleted-database-tutorial-management-portal.md)」を参照してください。

## 方法: 削除済みの Azure SQL データベースを REST API を使用して復元する

1.	「[削除された復元可能なデータベースの一覧表示](http://msdn.microsoft.com/library/azure/dn509562.aspx)」の操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
	
2.	「[削除された復元可能なデータベースの取得](http://msdn.microsoft.com/library/azure/dn509574.aspx)」の操作を使用して、復元する削除済みデータベースの詳細を取得します。

3.	「[データベースの復元要求の作成](http://msdn.microsoft.com/library/azure/dn509571.aspx)」の操作を使用して、復元を開始します。
	
4.	「[データベース操作の状態](http://msdn.microsoft.com/library/azure/dn720371.aspx)」の操作を使用して、復元の状態を追跡します。

## 次のステップ

詳細については、次のトピックを参照してください。

[Azure SQL データベースの継続性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database のバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[サービス管理 REST API リファレンス](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=July15_HO4-->