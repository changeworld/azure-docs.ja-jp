<properties
   pageTitle="クラウドのビジネス継続性 - ポイントインタイム リストア - SQL Database | Microsoft Azure"
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
   ms.date="05/10/2016"
   ms.author="sstein"/>

# 概要: SQL Database のポイントインタイム リストア

> [AZURE.SELECTOR]
- [概要](sql-database-point-in-time-restore.md)
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

ポイントインタイム リストアでは、[SQL Database 自動バックアップ](sql-database-automated-backups.md)を使用して、データベースを以前の特定の時点に復元できます。以前の時点への復元には、[Azure ポータル](sql-database-point-in-time-restore-portal.md)、[PowerShell](sql-database-point-in-time-restore-powershell.md)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用できます。

データベースは、任意のパフォーマンス レベルまたはエラスティック プールに復元できます。復元によって新しいデータベースが作成される点や、復元されるデータベースのサービス レベルとパフォーマンス レベルが、現在実行中のデータベースの状態とは異なる点を考慮して、サーバーまたはプールに十分な DTU クォータがあることを確認する必要があります。復元が完了すると、復元されたデータベースは、通常の完全にアクセス可能なオンライン データベースになり、サービス レベルとパフォーマンス レベルに基づいて通常料金が発生します。復旧の目的でデータベースを復元する場合、復元されたデータベースを元のデータベースの代わりとして扱うか、データを取得し、元のデータベースを更新するために使用することができます。復元されたデータベースを元のデータベースの代わりに使用する場合、パフォーマンス レベルやサービス レベルが適切であることを確認し、必要に応じてデータベースをスケールしてください。T-SQL の ALTER DATABASE コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。復元されたデータベースからデータを取得する場合、何らかのデータ復旧スクリプトを作成し、実行する必要があります。復元操作が完了するまでに時間がかかる可能性がありますが、データベース一覧全体でデータベースが表示されるようになります。復元中にデータベースを削除すると、操作は取り消され、料金はかかりません。

## ポイントインタイム リストアの復旧時間

データベースの復元にかかる時間は、データベースのサイズ、選択した時点、選択した時点の状態を再構築するために再生する必要があるアクティビティ数など、多くの要因で変わります。大規模なデータベースや、アクティビティ数が多いデータベースの場合、復元に数時間かかることがあります。データベースを復元すると、常に元のデータベースと同じサーバー上に新しいデータベースが作成されます。そのため、復元されるデータベースには、新しい名前を付ける必要があります。

## バックアップ/復元とコピー/エクスポート/インポート

ポイントインタイム リストアは、偶発的なデータの損失や破損から Basic、Standard、および Premium データベースを復旧する場合に推奨されるアプローチです。バックアップと復元は、低コスト (過度なサイズでなければ、バックアップに料金はかかりませんが、トランザクションに一貫性のあるエクスポートを確保する必要があるデータベース コピーや、BACPAC ファイルの格納の場合は料金がかかります)、管理作業ゼロ (バックアップは自動的に実行されますが、エクスポートの管理やスケジュールは管理者が行う必要があります) です。また、RPO に優れ (特定の時点への復元を、コピー/エクスポート/インポートのよりも細かい設定 (1 分) で行うことができます)、復元処理も短時間です (インポートは、スキーマの作成、インデックスの無効化、データの読み込み、各テーブルのインデックスの有効化という処理を伴うので、バックアップからの復元は、通常、インポートよりもはるかに高速です)。ただし、リテンション期間を超える長期のアーカイブの場合、今後もコピーとエクスポートが推奨されます。


## 概要

自動バックアップとポイントインタイム リストアを使用すると、偶発的なデータの破損や削除からデータベースを保護できます。このゼロ コスト、管理作業ゼロのソリューションは、すべての SQL Database で使用できます。短期間の復元のニーズがある場合、バックアップと復元には、代替のコピー/エクスポート/インポート ソリューションよりも大きな利点があります。ポイントインタイム リストアは、ビジネス継続性戦略の一環として使用し、長期のアーカイブやデータ移行目的で必要な場合にのみ、エクスポートを使用することをお勧めします。


## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [Azure ポータルを使用したポイントインタイム リストア](sql-database-point-in-time-restore-portal.md)
- [PowerShell を使用したポイントインタイム リストア](sql-database-point-in-time-restore-powershell.md)
- [REST API を使用したポイントインタイム リストア](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)


## その他のリソース

- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->