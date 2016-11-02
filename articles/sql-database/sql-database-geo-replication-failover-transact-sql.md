<properties 
    pageTitle="Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始 | Microsoft Azure" 
    description="Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始" 
    services="sql-database" 
    documentationCenter="" 
    authors="CarlRabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


この記事では、Transact-SQL を使用して、セカンダリ SQL Database へのフェールオーバーを開始する方法について説明します。geo レプリケーションを構成する場合は、[Azure SQL Database の geo レプリケーションの構成](sql-database-geo-replication-transact-sql.md)に関するページをご覧ください。



フェールオーバーを開始するには、以下が必要です。

- プライマリ上の DBManager であるログイン。geo レプリケートするローカル データベースの db\_ownership を所有し、geo レプリケーションを構成するパートナー サーバー上の DBManager になります。
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。




## セカンダリ データベースを昇格させて新しいプライマリにする、計画されたフェールオーバーを開始する

**ALTER DATABASE** ステートメントを使用すると、計画的にセカンダリ データベースを昇格させて新しいプライマリ データベースにすることができます。これにより、既存のプライマリはセカンダリに降格します。このステートメントは、昇格の対象となる geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバー上の master データベースに対して実行されます。この機能は、DR ドリル時など、計画されたフェールオーバー用に設計されており、プライマリ データベースが使用できることが必要条件となります。

コマンドによって、次のワークフローが実行されます。

1. 一時的にレプリケーションを同期モードに切り替えます。未処理のトランザクションはすべてセカンダリにフラッシュされ、新しいトランザクションはすべてブロックされます。

2. geo レプリケーション パートナーシップにおける 2 つのデータベースのロールを切り替えます。

このシーケンスにより、ロールの切り替えの前に 2 つのデータベースが同期されることが保証されるため、データ損失は発生しません。ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。通常の状況では、操作全体が完了するのに 1 分かかりません。詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](sql-database-service-tiers.md)に関するページを参照してください。


計画されたフェールオーバーを開始するには、次の手順に従います。

1. Management Studio で、geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、セカンダリ データベースをプライマリ ロールに切り替えます。

        ALTER DATABASE <MyDB> FAILOVER;

4. **[実行]** をクリックしてクエリを実行します。

>[AZURE.NOTE] まれに、操作が完了しないで、スタックしたような状態になる場合があります。この場合、ユーザーは強制フェールオーバー コマンドを実行できますが、データが失われることを容認する必要があります。


## プライマリ データベースからセカンダリ データベースへの計画されていないフェールオーバーを開始します。

**ALTER DATABASE** ステートメントを使用すると、計画外にセカンダリ データベースを昇格させて新しいプライマリ データベースにすることができます。これにより、プライマリ データベースが使用できなくなると、既存のプライマリが強制的にセカンダリに降格されます。このステートメントは、昇格の対象となる geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバー上の master データベースに対して実行されます。

この機能は、データベースの可用性を復元することが重要で、多少のデータ損失は許容されるような障害復旧を目的として設計されています。強制フェールオーバーが呼び出されると、指定したセカンダリ データベースはすぐにプライマリ データベースになり、書き込みトランザクションの受け入れを開始します。元のプライマリ データベースがこの新しいプライマリ データベースと再接続できるようになるとすぐに、元のプライマリ データベース上で増分バックアップが行われます。古いプライマリ データベースは新しいプライマリ データベースのセカンダリ データベースとなり、それ以降、新しいプライマリの単なる同期用レプリカになります。

ただし、セカンダリ データベースではポイントインタイム リストアがサポートされていません。そのため、古いプライマリ データベースにコミットされたデータのうち、強制フェールオーバーが発生する前に新しいプライマリ データベースにレプリケートされなかったデータを復元しようとする場合、ユーザーは、この失われたデータを復元するようサポートに問い合わせる必要があります。

プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。

計画されていないフェールオーバーを開始するには、次の手順に従います。

1. Management Studio で、geo レプリケートされたセカンダリ データベースが存在する Azure SQL Database 論理サーバーに接続します。

2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。

3. 次の **ALTER DATABASE** ステートメントを使用して、セカンダリ データベースをプライマリ ロールに切り替えます。

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. **[実行]** をクリックしてクエリを実行します。

>[AZURE.NOTE] プライマリとセカンダリの両方がオンラインのときにコマンドを発行すると、データが同期されずに、古いプライマリがすぐに新しいセカンダリになります。コマンドが発行されたときにプライマリがトランザクションをコミット中の場合、一部のデータが失われる可能性があります。



## 次のステップ   

- フェールオーバー後は、サーバーおよびデータベースの認証要件が新しいプライマリで構成されていることを確認してください。詳細については、[障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関するページを参照してください。
- 復旧前および復旧後の手順、障害復旧訓練の実施など、アクティブ geo レプリケーションを使用した障害復旧については、[障害復旧](sql-database-disaster-recovery.md)に関するページをご覧ください。
- アクティブ geo レプリケーションについては、Sasha Nosov の [geo レプリケーションの新機能](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)に関するブログ記事をご覧ください。
- アクティブ geo レプリケーションを使用したクラウド アプリケーションの設計については、[geo レプリケーションを使用したビジネス継続性のためのクラウド アプリケーション設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)に関するページをご覧ください。
- エラスティック データベース プールでのアクティブ geo レプリケーションの使用については、[エラスティック プールの障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)に関するページをご覧ください。
- ビジネス継続性の概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0831_2016-->