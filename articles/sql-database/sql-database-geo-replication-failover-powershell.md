<properties 
    pageTitle="PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 | Microsoft Azure" 
    description="PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# PowerShell を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始



> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


この記事では、PowerShell を使用して、SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーを開始する方法について説明します。geo レプリケーションを構成する場合は、[Azure SQL Database の geo レプリケーションの構成](sql-database-geo-replication-powershell.md)に関するページをご覧ください。



## 計画されたフェールオーバーの開始

セカンダリ データベースを昇格させて新しいプライマリ データベースとし、既存のプライマリ データベースを降格させてセカンダリ データベースにするには、**Set-AzureRmSqlDatabaseSecondary** コマンドレットと **-Failover** パラメーターを組み合わせて使用します。　　　この機能は、障害復旧の訓練時など、計画されたフェールオーバー用に設計されたものであり、プライマリ データベースを使用できることが必要条件となります。

コマンドによって、次のワークフローが実行されます。

1. 一時的にレプリケーションを同期モードに切り替えます。これにより、すべての未完了のトランザクションがセカンダリ データベースにフラッシュされます。

2. geo レプリケーション パートナーシップで 2 つのデータベースのロールを切り替えます。

このシーケンスにより、ロールの切り替えの前に 2 つのデータベースが同期されることが保証されるため、データ損失は発生しません。ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。通常の状況では、操作全体が完了するのに 1 分かかりません。詳細については、「[Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)」を参照してください。




このコマンドレットは、セカンダリ データベースをプライマリに切り替えるプロセスが完了すると、戻ります。

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" 上の "mydb" という名前のデータベースのロールをプライマリに切り替えます。"db2" の接続先である元のプライマリは、2 つのデータベースが完全に同期すると、セカンダリに切り替わります。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] まれに、操作を完了できず、応答していないように見える場合があります。この場合、ユーザーは強制フェールオーバー コマンド (計画されていないフェールオーバー) を呼び出すことができますが、データは失われることを容認する必要があります。


## プライマリ データベースからセカンダリ データベースへの計画されていないフェールオーバーを開始します。


計画外のフェールオーバーとして、既存のプライマリ データベースが使用できなくなった時点でセカンダリ データベースを昇格させて新しいプライマリ データベースとし、既存のプライマリ データベースを降格させてセカンダリ データベースにするには、**Set-AzureRmSqlDatabaseSecondary** コマンドレットを **–Failover** および **-AllowDataLoss** パラメーターと組み合わせて使用します。

この機能は、データベースの可用性を復元することが重要で、多少のデータ損失は許容されるような障害復旧を目的として設計されています。強制フェールオーバーが呼び出されると、指定したセカンダリ データベースはすぐにプライマリ データベースになり、書き込みトランザクションの受け入れを開始します。強制フェールオーバー操作の後、元のプライマリ データベースとこの新しいプライマリ データベースとの再接続が可能になるとすぐに、元のプライマリ データベース上で増分バックアップが行われます。古いプライマリ データベースは新しいプライマリ データベースのセカンダリ データベースとされ、以後、新しいプライマリ データベースの単なるレプリカとなります。

ただし、セカンダリ データベース上でポイントインタイム リストアはサポートされていないので、古いプライマリ データベースにコミットされているデータのうち、新しいプライマリ データベースにレプリケートされていないものを復旧する場合は、CSS を有効にして既知のログ バックアップにデータベースを復元する必要があります。

> [AZURE.NOTE] プライマリとセカンダリの両方がオンラインのときにコマンドを発行すると、データが同期されずに、古いプライマリがすぐに新しいセカンダリになります。コマンドが発行されたときにプライマリがトランザクションをコミット中の場合、一部のデータが失われる可能性があります。


プライマリ データベースに複数のセカンダリ データベースがある場合、コマンドは部分的に成功します。コマンドが実行されたセカンダリ データベースがプライマリ データベースになります。ただし、古いプライマリ データベースはプライマリ データベースのままです。すなわち、2 つのプライマリ データベースは不整合の状態になり、中断されたレプリケーション リンクによって接続されます。ユーザーは、この 2 つのプライマリ データベースのいずれかで “remove secondary” API を使用して、この構成を手動で修復する必要があります。


次のコマンドでは、プライマリが利用できない場合に、"mydb" という名前のデータベースのロールをプライマリに切り替えます。"mydb" の接続先であった元のプライマリ データベースは、それがオンラインに戻ると、セカンダリ データベースに切り替わります。その時点で、同期化によってデータが失われる可能性があります。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## その他のリソース

- [新しい geo レプリケーション機能に関するスポットライト](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Geo レプリケーションを使用してビジネス継続性を実現するクラウド アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)

<!---HONumber=AcomDC_0615_2016-->