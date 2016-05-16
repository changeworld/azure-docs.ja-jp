<properties 
    pageTitle="Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 | Microsoft Azure" 
    description="Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Azure ポータルを使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md)


この記事では、[Azure ポータル](http://portal.azure.com)を使用して、セカンダリ SQL Database へのフェールオーバーを開始する方法について説明します。geo レプリケーションを構成する場合は、[Azure SQL Database の geo レプリケーションの構成](sql-database-geo-replication-portal.md)に関するページをご覧ください。


## フェールオーバーの開始

セカンダリ データベースは、プライマリ データベースとして使用するように切り替えることができます。

1. [Azure ポータル](http://portal.azure.com)で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. [SQL Database] ブレードで、**[すべての設定]**、**[geo レプリケーション]** の順に選択します。
3. **[セカンダリ]** ボックスの一覧で、新しいプライマリとして使用するデータベースを選択し、**[フェールオーバー]** をクリックします。

    ![フェールオーバー][2]

4. **[はい]** をクリックして、フェールオーバーを開始します。

このコマンドは、セカンダリ データベースをプライマリ ロールに即座に切り替えます。

ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。通常の状況では、操作全体が完了するのに 1 分かかりません。

>[AZURE.NOTE] コマンドが発行されたときに、プライマリがオンラインでトランザクションをコミット中の場合、一部のデータが失われる可能性があります。


## その他のリソース   


- [災害復旧訓練](sql-database-disaster-recovery-drills.md)
- [新しい geo レプリケーション機能に関するスポットライト](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [geo レプリケーションを使用したビジネス継続性を実現するクラウド アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0504_2016-->