<properties 
	pageTitle="Azure の管理ポータルを使用した Azure SQL データベースの管理" 
	description="Azure の管理ポータルを使用して、クラウド内のリレーショナル データベースを管理する方法について説明します。" 
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
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# Azure の管理ポータルを使用した Azure SQL データベースの管理

[Azure の管理ポータル][Management Portal]を使用すると、Azure SQL データベースとサーバーの作成、監視、管理を実行できます。この記事では、管理ポータルを使用して実行できるデータベース操作について重点的に説明します。Azure SQL Database のその他の管理ツールの詳細については、[こちら][AzureDb management overview]を参照してください。

>[AZURE.NOTE]Azure の管理ポータルに慣れていない場合は、その全般的な機能と概念について、この[ビデオ ツアーで簡単な概要をご覧ください][Azure Portal Tour]。

![Database Overview](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1.データベースの管理操作
![Db management actions](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure の管理ポータルには、データベースのブレードの上部からアクセスできる、一般的なデータベース操作が用意されています。データベースは、以前の状態に復元したり、Visual Studio で開いたり、新しいサーバーにコピーしたり、Azure ストレージ アカウントにエクスポートしたりできます。

## 2.データベースの監視
![Database monitoring](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL データベースには、既定で、データベース スループット単位 (DTU)、データベース サイズ、接続の正常性の監視グラフが備わっています。これらの監視グラフは、CPU の割合、データ IO の割合、デッドロック、ログ IO の割合、またはファイアウォールによってブロックされた要求の割合のグラフも作成するようにカスタマイズおよび拡張できます。監視グラフをカスタマイズする方法の詳細については、[こちら][Azure part monitoring]を参照してください。

さらに、アラート ルールは、指定したメトリックを監視して、事前に設定されたしきい値に達したときに指定の管理者と共同管理者に警告するように設定できます。Azure の管理ポータルでアラート ルールを設定する方法の詳細については、[こちら][Azure part monitoring]を参照してください。

## 3.データベースのセキュリティと監査
![Database security](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL データベースは、すべてのデータベース イベントを追跡し、そのイベントを Azure ストレージ アカウントの監査ログに書き込むように構成できます。この機能により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異に対する洞察が容易になります。Azure SQL Database の監査の詳細については、[こちら][AzureDb Auditing]を参照してください。

また、Azure SQL データベースは、権限を持たないユーザーに対して機密データをマスクするように構成することもできます。Azure SQL Database の動的データ マスク機能の詳細については、[こちら][AzureDb datamasking]を参照してください。

## 4.geo レプリケーション
![Geo-replication](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL データベースは、コミットされたトランザクションをセカンダリ データベースに非同期にレプリケートするように構成できます。管理ポータルの geo レプリケーション パートでは、セカンダリ データベースを配置する Azure リージョンを選択できます。Azure のデータベースの geo レプリケーションの詳細については、[こちら][Database geo-replication]を参照してください。

##その他のリソース
* [SQL Database の概要][]   
* [SQL Server Management Studio を使用した Azure SQL Database の管理][]   
* [動的管理ビューを使用した SQL Database の監視][]   
* [Transact-SQL リファレンス (SQL Database)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [SQL Database の概要]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [SQL Server Management Studio を使用した Azure SQL Database の管理]: sql-database-manage-azure-ssms.md
  [動的管理ビューを使用した SQL Database の監視]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Transact-SQL リファレンス (SQL Database)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 

<!---HONumber=58--> 