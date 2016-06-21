<properties
	pageTitle="Azure ポータルを使用した Azure SQL データベースの管理"
	description="Azure ポータルを使用して、クラウド内のリレーショナル データベースを管理する方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="05/20/2016"
	ms.author="sstein"/>


# Azure ポータルを使用した Azure SQL データベースの管理


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

[Azure ポータル](https://portal.azure.com/)では、Azure SQL Database と論理サーバーを作成、監視、管理できます。この記事では、いくつかの一般的なタスクについて説明します。

![Database Overview](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\.データベースの管理操作

![Db management actions](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure ポータルには、データベースのブレードの上部からアクセスできる、一般的なデータベース操作が用意されています。データベースは、以前の状態に復元したり、Visual Studio で開いたり、新しいサーバーにコピーしたり、Azure ストレージ アカウントにエクスポートしたりできます。

- [SQL Database を復元する](sql-database-user-error-recovery.md)
- [Visual Studio で SQL Database を開く](sql-database-connect-query.md)
- [SQL Database をエクスポートする](sql-database-export.md)

## 2\.データベースの監視

![Database monitoring](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL Database は、データベース トランザクション ユニット (DTU)、データベース サイズ、接続の正常性の監視グラフを既定で備えています。これらの監視グラフは、CPU の割合、データ IO の割合、デッドロック、ログ IO の割合、またはファイアウォールによってブロックされた要求の割合のグラフも作成するようにカスタマイズおよび拡張できます。

さらに、アラート ルールは、指定したメトリックを監視して、事前に設定されたしきい値に達したときに指定の管理者と共同管理者に警告するように設定できます。

## 3\.データベースのセキュリティと監査

![Database security](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL Database は、すべてのデータベース イベントを追跡し、そのイベントを Azure ストレージ アカウントの監査ログに書き込むように構成できます。この機能により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異に対する洞察が容易になります。

- [SQL Database の監査](sql-database-auditing-get-started.md)

また、Azure SQL データベースは、権限を持たないユーザーに対して機密データをマスクするように構成することもできます。

- [動的データ マスク](sql-database-dynamic-data-masking-get-started.md)


## 4\.geo レプリケーション

![geo レプリケーション](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL Database は、コミットされたトランザクションをセカンダリ データベースに非同期にレプリケートするように構成できます。ポータルの geo レプリケーション パートでは、セカンダリ データベースを配置する Azure リージョンを選択できます。

- [geo レプリケーション](sql-database-geo-replication-overview.md)



## その他のリソース

- [SQL Database](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0608_2016-->