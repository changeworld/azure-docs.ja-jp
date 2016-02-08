<properties
	pageTitle="Azure クラシック ポータルを使用した Azure SQL Database の管理"
	description="Azure クラシック ポータルを使用して、クラウド内のリレーショナル データベースを管理する方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="01/22/2016"
	ms.author="sstein"/>


# Azure ポータルを使用した Azure SQL データベースの管理


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

[Azure ポータル](https://portal.azure.com/)を使用すると、Azure SQL データベースと論理サーバーを作成、監視、管理できます。この記事では、いくつかの一般的なタスクについて説明します。

![Database Overview](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\.データベースの管理操作
![Db management actions](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure ポータルには、データベースのブレードの上部からアクセスできる、一般的なデータベース操作が用意されています。データベースは、以前の状態に復元したり、Visual Studio で開いたり、新しいサーバーにコピーしたり、Azure ストレージ アカウントにエクスポートしたりできます。

- [SQL Database を復元する](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Visual Studio で SQL Database を開く](sql-database-connect-query.md)
- [SQL Database をエクスポートする](sql-database-export.md)

## 2\.データベースの監視
![Database monitoring](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL Database には、既定で、データベース スループット単位 (DTU)、データベース サイズ、接続の正常性の監視グラフが備わっています。これらの監視グラフは、CPU の割合、データ IO の割合、デッドロック、ログ IO の割合、またはファイアウォールによってブロックされた要求の割合のグラフも作成するようにカスタマイズおよび拡張できます。監視グラフをカスタマイズする方法の詳細については、[こちら][Azure part monitoring]を参照してください。

さらに、アラート ルールは、指定したメトリックを監視して、事前に設定されたしきい値に達したときに指定の管理者と共同管理者に警告するように設定できます。Azure クラシック ポータルでアラート ルールを設定する方法の詳細については、[こちら][Azure part monitoring]を参照してください。

## 3\.データベースのセキュリティと監査
![Database security](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL Database は、すべてのデータベース イベントを追跡し、そのイベントを Azure ストレージ アカウントの監査ログに書き込むように構成できます。この機能により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異に対する洞察が容易になります。

- [SQL Database の監査](sql-database-auditing-get-started.md)

また、Azure SQL データベースは、権限を持たないユーザーに対して機密データをマスクするように構成することもできます。

- [動的データ マスク](sql-database-dynamic-data-masking-get-started.md)


## 4\.geo レプリケーション
![geo レプリケーション](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL Database は、コミットされたトランザクションをセカンダリ データベースに非同期にレプリケートするように構成できます。ポータルの geo レプリケーション パートでは、セカンダリ データベースを配置する Azure リージョンを選択できます。

- [geo レプリケーション](https://msdn.microsoft.com/library/azure/dn783447.aspx)





##その他のリソース
* [SQL Database](sql-database-technical-overview.md)   
* [動的管理ビューを使用した SQL Database の監視][]   
* [Transact-SQL リファレンス (SQL Database)][]

  [Azure Classic Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Classic Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introducing SQL Database]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Managing Azure SQL Database using SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [動的管理ビューを使用した SQL Database の監視]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Transact-SQL リファレンス (SQL Database)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

<!---HONumber=AcomDC_0128_2016-->