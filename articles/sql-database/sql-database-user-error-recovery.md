<properties 
   pageTitle="SQL Database のユーザー エラーの復旧 | Microsoft Azure" 
   description="Azure SQL Database の特定時点に復元 (PITR) 機能を使用して、ユーザー エラー、偶発的なデータの破損、または削除済みデータベースを復旧する方法について説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# ユーザー エラーからの Azure SQL Database の復旧

Azure SQL Database は、ユーザー エラーまたは予期しないデータ変更から復旧するための、2 つの中核的な機能を提供します。

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md) 
- [削除済みデータベースの復元](sql-database-restore-deleted-database.md)

Azure SQL Database では、常に新しいデータベースに復元します。これらの復元機能は、Basic、Standard、Premium のすべてのデータベースに提供されます。

##ポイントインタイム リストア

ユーザー エラーや意図しないデータ変更が発生した場合、ポイントインタイム リストア (PITR) 機能を使用して、データベースの保持期間内の任意の時点にデータベースを復元できます。

Basic データベースの保持期間は 7 日間、Standard データベースの保持期間は 14 日間、Premium データベースの保持期間は 35 日です。データベース バックアップのリテンション期間の詳細については、[自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

ポイントインタイム リストアを実行する場合は、次をご覧ください。

- [Azure ポータルでのポイントインタイム リストア](sql-database-point-in-time-restore-portal.md)
- [PowerShell でのポイントインタイム リストア](sql-database-point-in-time-restore-powershell.md)
- [REST API でのポイントインタイム リストア (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## 削除されたデータベースの復元

データベースが削除された場合、Azure SQL Database では、削除されたデータベースを削除された時点の状態に復元できます。Azure SQL Database では、データベースの保持期間にわたり、削除されたデータベースのバックアップを格納します。

削除されたデータベースの保持期間は、データベースのサービス階層に基づく日数とデータベースが存在していた日数のどちらか短い方になります。データベースのリテンション期間の詳細については、[自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

削除されたデータベースを復元するには:

- [Azure ポータルで削除済みデータベースを復元する](sql-database-restore-deleted-database-portal.md)
- [PowerShell で削除済みデータベースを復元する](sql-database-restore-deleted-database-powershell.md)
- [REST API で削除済みデータベースを復元する (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## 次のステップ

- 障害復旧のためのアクティブ geo レプリケーションの使用および構成の方法については、「[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)」をご覧ください。
- 障害復旧のための geo リストアの使用方法については、「[geo リストア](sql-database-geo-restore.md)」をご覧ください。

## その他のリソース

- [概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (概要: SQL Database のポイントインタイム リストア)](sql-database-point-in-time-restore.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->