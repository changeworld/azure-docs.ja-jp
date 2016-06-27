<properties
   pageTitle="トランザクション レプリケーションを使用して SQL Database に移行する | Microsoft Azure"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのインポート、トランザクション レプリケーション"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# トランザクション レプリケーションを使用して SQL Server データベースを Azure SQL Database に移行する

> [AZURE.SELECTOR]
- [SSMS の移行ウィザード](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [BACPAC ファイルへのエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [BACPAC ファイルからのインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [トランザクション レプリケーション](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

この記事では、SQL Server のトランザクション レプリケーションを使用して最小限のダウンタイムで Azure SQL Database へ互換性のある SQL Server データベースを移行する方法について説明します。

## トランザクション レプリケーションのアーキテクチャについて

移行中、SQL Server データベースを外す余裕がない場合、移行ソリューションとして SQL Server トランザクション レプリケーションを使用できます。このソリューションを使用するには、Azure SQL Database を移行対象のオンプレミス SQL Server インスタンスへのサブスクライバーとして構成します。オンプレミスのトランザクション レプリケーション ディストリビューターは、新しいトランザクションが発生し続ける中で、同期対象のオンプレミスのデータベース (パブリッシャー) からデータを同期します。

また、トランザクション レプリケーションを使用し、オンプレミス データベースの一部を移行できます。Azure SQL Database に複製するパブリケーションは、複製されるデータベースのテーブルの一部に制限できます。また、複製されるテーブルごとに、行の一部または列の一部にデータを制限できます。

トランザクション レプリケーションでは、移行を開始した瞬間から移行を完了した瞬間までに発生したデータやスキーマの変更が Azure SQL Database に表示されます。

同期が完了し、移行の準備ができたら、アプリケーションの接続文字列を変更し、オンプレミス データベースの代わりに Azure SQL Database にポイントします。トランザクション レプリケーションがオンプレミス データベースに残っているすべての変更を抜き取り、すべてのアプリケーションが Azure DB をポイントしたら、Azure SQL Database を本稼働システムとして残し、レプリケーションを安全にアンインストールできます。

 ![SeedCloudTR ダイアグラム](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## トランザクション レプリケーションの要件

トランザクション レプリケーションは、SQL Server 6.5 以降、SQL Server に内蔵された技術です。非常に成熟した、証明済みの技術であり、ほとんどの DBA に運用経験があります。[SQL Server 2016 プレビュー](http://www.microsoft.com/server-cloud/products/sql-server-2016/)では、Azure SQL Database をオンプレミス パブリケーションの[トランザクション レプリケーション サブスクライバー](https://msdn.microsoft.com/library/mt589530.aspx)として構成できるようになりました。Management Studio の設定は、オンプレミス サーバーでトランザクション レプリケーション サブスクライバーを設定する場合とまったく同じです。このシナリオは、パブリッシャーとディストリビューターの SQL Server のバージョンが次のいずれか以上である場合にサポートされます。

 - SQL Server 2016 CTP3 (プレビュー) 以降 
 - SQL Server 2014 SP1 CU3 以降
 - SQL Server 2014 RTM CU10 以降
 - SQL Server 2012 SP2 CU8 以降
 - SQL Server 2013 SP3 のリリース時


> [AZURE.IMPORTANT] 最新バージョンの SQL Server Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持する必要があります。以前のバージョンの SQL Server Management Studio では、サブスクライバーとして SQL Database を設定できません。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。


## 次のステップ

- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016 Preview](http://www.microsoft.com/server-cloud/products/sql-server-2016/)

## その他のリソース

- [トランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->