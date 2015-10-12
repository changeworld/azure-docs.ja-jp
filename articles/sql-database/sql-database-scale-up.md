<properties
	pageTitle="Azure SQL Database のサービス階層とパフォーマンス レベルを変更する"
	description="「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、SQL Databaseのスケール アップとスケール ダウンの方法について説明しています。Azure SQL Database の価格レベルを変更します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

この記事では、[Azure プレビュー ポータル](https://portal.azure.com)で SQL Database のサービス階層とパフォーマンス レベルを変更する方法について説明します。

[SQL Database の Web/Business データベースの新しいサービス階層へのアップグレード](sql-database-upgrade-new-service-tiers.md)に関するトピックと「[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)」の情報を使用して、Azure SQL Database に適したサービス階層とパフォーマンス レベルを決定します。

> [AZURE.IMPORTANT]SQL Database のサービス階層とパフォーマンス レベルの変更はオンライン操作です。そのため、操作中はダウンタイムがなく、データベースを使用できます。

- データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
- [標準の geo レプリケーション](https://msdn.microsoft.com/library/azure/dn758204.aspx)または[アクティブ geo レプリケーション](https://msdn.microsoft.com/library/azure/dn741339.aspx)を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス階層にアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
- Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。「[連続コピーの関係の終了](https://msdn.microsoft.com/library/azure/dn741323.aspx)」に説明されている手順に従って、プライマリ データベースとアクティブ セカンダリ データベース間のレプリケーション プロセスを停止してください。
- サービス階層によって、提供されている復元サービスは異なります。ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。詳細については、「[Azure SQL Database のバックアップと復元](https://msdn.microsoft.com/library/azure/jj650016.aspx)」を参照してください。
- 24 時間の期間内に 4 つまでの個別のデータベースの変更 (サービス階層またはパフォーマンス レベル) を行うことができます。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。


**この記事を完了するには、以下が必要です。**

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。


## データベースのサービス階層とパフォーマンス レベルを変更する


スケールアップまたはスケールダウンするデータベースの [SQL Database] ブレードを開きます。

1.	[Azure プレビュー ポータル](https://portal.azure.com)にアクセスします。
2.	**[すべて参照]** をクリックします。
3.	**[SQL Database]** をクリックします。
2.	変更するデータベースをクリックします。
3.	[SQL Database] ブレードで **[価格レベル]** をクリックします。

    ![価格タイル][1]

1.  新しい階層を選択し、**[選択]** をクリックします。

    **[選択]** をクリックすると、データベース階層を変更するスケール要求が送信されます。データベースのサイズに応じて、スケール操作の完了に時間がかかる場合があります。通知をクリックすると、スケール操作の詳細と状態が表示されます。

    ![価格レベルの選択][2]

3.	左側のリボンにある **[通知]** をクリックします。

    ![通知][3]

## データベースが選択した価格レベルであることを確認する

   スケール操作が完了したら、データベースが目的の階層になっていることを確認します。

2.	**[すべて参照]** をクリックします。
3.	**[SQL Database]** をクリックします。
2.	更新したデータベースをクリックします。
3.	**[価格レベル]** タイルで、正しい階層に設定されていることを確認します。

    ![新しい価格][4]


## 次のステップ

- [スケール アウトとスケール イン](sql-database-elastic-scale-get-started.md)
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

<!---HONumber=Oct15_HO1-->