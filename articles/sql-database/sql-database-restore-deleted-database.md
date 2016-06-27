<properties
   pageTitle="クラウドのビジネス継続性 - 削除済みデータベースの復元 - SQL Database | Microsoft Azure"
   description="Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 概要: 削除済み Azure SQL Database の復元

> [AZURE.SELECTOR]
- [概要](sql-database-restore-deleted-database.md)
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

削除されたデータベースは、[SQL Database 自動バックアップ](sql-database-automated-backups.md)のリテンション期間中に復元できます。[Azure ポータル](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用することができます。

データベースを削除すると、最後のバックアップは、通常のリテンション期間維持されるため、削除された時点までデータベースを復元できます。

## 最近削除されたデータベースの復元

削除されたデータベースの場合、復元ポイントはデータベースの削除時点に固定されます。削除されたデータベースを復元する際は、そのデータベースは、元のデータベースを格納していたサーバーにのみ復元できます。サーバーを削除するときは、この点に気を付けてください。サーバーを削除すると、そのサーバーに以前にあったデータベースを復元できなくなります。

> [AZURE.IMPORTANT] Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。

## 概要

自動バックアップは、偶発的なデータベース削除からデータベースを保護します。このゼロ コスト、管理作業ゼロのソリューションは、すべての SQL Database で使用できます。

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [Azure ポータルを使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-portal.md)
- [PowerShell を使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-powershell.md)
- [REST API を使用して削除済みデータベースを復元する](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)

## その他のリソース

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->