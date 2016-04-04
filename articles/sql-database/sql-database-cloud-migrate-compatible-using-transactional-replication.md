<properties
   pageTitle="トランザクション レプリケーションを使用して SQL Database に移行する"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのインポート、トランザクション レプリケーション"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# トランザクション レプリケーションを使用して SQL Server データベースを SQL Database に移行する

移行中、SQL Server データベースを外す余裕がない場合、移行ソリューションとして SQL Server トランザクション レプリケーションを使用できます。トランザクション レプリケーションでは、移行を開始した瞬間から移行を完了した瞬間までに発生したデータやスキーマの変更が Azure SQL Database に表示されます。移行が完了したら、アプリケーションの接続文字列を変更し、オンプレミス データベースの代わりに Azure SQL Database にポイントします。トランザクション レプリケーションがオンプレミス データベースに残っているすべての変更を抜き取り、すべてのアプリケーションが Azure DB をポイントしたら、Azure SQL Database を本稼働システムとして残し、レプリケーションを安全にアンインストールできます。

 ![SeedCloudTR ダイアグラム](./media/sql-database-cloud-migrate/SeedCloudTR.png)


トランザクション レプリケーションは、SQL Server 6.5 以降、SQL Server に内蔵された技術です。非常に成熟した、証明済みの技術であり、ほとんどの DBA に運用経験があります。[SQL Server 2016 プレビュー](http://www.microsoft.com/server-cloud/products/sql-server-2016/)では、Azure SQL Database をオンプレミス パブリケーションの[トランザクション レプリケーション サブスクライバー](https://msdn.microsoft.com/library/mt589530.aspx)として構成できるようになりました。Management Studio の設定は、オンプレミス サーバーでトランザクション レプリケーション サブスクライバーを設定する場合とまったく同じです。このシナリオは、パブリッシャーとディストリビューターの SQL Server のバージョンが次のいずれか以上である場合にサポートされます。

 - SQL Server 2016 CTP3 (プレビュー) 以降 
 - SQL Server 2014 SP1 CU3 以降
 - SQL Server 2014 RTM CU10 以降
 - SQL Server 2012 SP2 CU8 以降
 - SQL Server 2013 SP3 のリリース時

また、トランザクション レプリケーションを使用し、オンプレミス データベースの一部を移行できます。Azure SQL Database に複製するパブリケーションは、複製されるデータベースのテーブルの一部に制限できます。また、複製されるテーブルごとに、行の一部または列の一部にデータを制限できます。

<!---HONumber=AcomDC_0316_2016-->