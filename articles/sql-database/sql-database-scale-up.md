<properties
	pageTitle="Azure SQL Database のサービス階層とパフォーマンス レベルを変更する"
	description="「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、SQL Databaseのスケール アップとスケール ダウンの方法について説明しています。Azure SQL Database の価格レベルを変更します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


サービス レベルとパフォーマンス レベルは、SQL Database で利用できる機能とリソースを表しており、アプリケーションのニーズの変化に応じて更新できます。詳細については、「[サービス レベル](sql-database-service-tiers.md)」をご覧ください。

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。ごくまれですが、特に、接続が無効になった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。


[SQL Database の Web/Business データベースの新しいサービス階層へのアップグレード](sql-database-upgrade-server-portal.md)に関するトピックと「[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)」の情報を使用して、Azure SQL Database に適したサービス階層とパフォーマンス レベルを決定します。

- データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
- [geo レプリケーション](sql-database-geo-replication-overview.md)を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
- サービス階層をダウングレードするときは、最初に geo レプリケーションのリレーションシップをすべて終了する必要があります。 
- サービス階層によって、提供されている復元サービスは異なります。ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。詳細については、「[Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
- データベースの価格レベルを変更しても、データベースの最大サイズは変更されません。データベースの最大サイズを変更するには、[Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) または [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) を使用します。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。



**この記事を完了するには、以下が必要です。**

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。


## データベースのサービス階層とパフォーマンス レベルを変更する


スケールアップまたはスケールダウンするデータベースの [SQL Database] ブレードを開きます。

1.	[Azure ポータル](https://portal.azure.com)にアクセスします。
2.	**[すべて参照]** をクリックします。
3.	**[SQL Database]** をクリックします。
2.	変更するデータベースをクリックします。
3.	SQL Database ブレードで、**[すべての設定]** をクリックし、**[価格レベル (DTU のスケール)]** をクリックします。

    ![価格レベル][1]


1.  新しい階層を選択し、**[選択]** をクリックします。

    **[選択]** をクリックすると、データベース階層を変更するスケール要求が送信されます。データベースのサイズに応じて、スケール操作の完了に時間がかかる場合があります。通知をクリックすると、スケール操作の詳細と状態が表示されます。

    > [AZURE.NOTE] データベースの価格レベルを変更しても、データベースの最大サイズは変更されません。データベースの最大サイズを変更するには、[Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) または [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) を使用します。

    ![価格レベルの選択][2]

3.	左側のリボンにある **[通知]** をクリックします。

    ![通知][3]

## データベースが選択した価格レベルであることを確認する

   スケール操作が完了したら、データベースが目的の階層になっていることを確認します。

2.	**[すべて参照]** をクリックします。
3.	**[SQL Database]** をクリックします。
2.	更新したデータベースをクリックします。
3.	**[価格レベル]** で、データベースが正しいレベルに設定されていることを確認します。

    ![新しい価格][4]


## 次のステップ

- [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) または [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) を使用して、データベースの最大サイズを変更します。
- [スケールアウトとスケールイン](sql-database-elastic-scale-get-started.md)
- [SSMS での SQL Database の接続とクエリ](sql-database-connect-query-ssms.md)
- [Azure SQL Database のエクスポート](sql-database-export.md)

## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0608_2016-->