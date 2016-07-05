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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 概要: 削除済み Azure SQL Database の復元

> [AZURE.SELECTOR]
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [削除済みデータベースの復元](sql-database-restore-deleted-database.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
- [ビジネス継続性のシナリオ](sql-database-business-continuity-scenarios.md)


削除されたデータベースは、ご利用の[サービス層](sql-database-service-tiers.md)の [SQL Database 自動バックアップ](sql-database-automated-backups.md)のリテンション期間中に復元できます。[Azure ポータル](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用することができます。

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 最近削除されたデータベースの復元

削除されたデータベースは、同じまたは別のデータベース名を使用して、元のデータベースを格納していた論理サーバーに復元できます。削除されたデータベースの場合、復元ポイントはデータベースの削除時点に固定されます。

> [AZURE.IMPORTANT] Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。

## 復元時間

データベースの復元にかかる時間は、データベースのサイズ、トランザクション ログの数、選択した時点、選択した時点の状態を再構築するために再生する必要があるアクティビティ数など、多くの要因で変わります。大規模なデータベースや、アクティビティ数が多いデータベースの場合、復元に数時間かかることがあります。データベースを復元すると、常に元のデータベースと同じサーバー上に新しいデータベースが作成されます。そのため、復元されるデータベースには、新しい名前を付ける必要があります。データベースの復元のほとんどは、12 時間以内に完了します。

## 概要

自動バックアップは、偶発的なデータベース削除からデータベースを保護します。このゼロ コスト、管理作業ゼロのソリューションは、すべての SQL Database で使用できます。

## 次のステップ

- Azure ポータルを使用して、削除されたデータベースを復元する方法の詳細な手順については、[Azure ポータルを使用した削除済みデータベースの復元](sql-database-restore-deleted-database-portal.md)に関するページをご覧ください。
- PowerShell を使用して、削除されたデータベースを復元する方法の詳細な手順については、[PowerShell を使用した削除済みデータベースの復元](sql-database-restore-deleted-database-powershell.md)に関するページをご覧ください。
- 削除されたデータベースを復元する方法については、[REST API を使用した削除済みデータベースの復元](https://msdn.microsoft.com/library/azure/mt163685.aspx)に関するページをご覧ください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」をご覧ください。

## その他のリソース

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->