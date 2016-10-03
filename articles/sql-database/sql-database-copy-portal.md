<properties
	pageTitle="Azure ポータルを使用した Azure SQL Database のコピー | Microsoft Azure"
	description="Azure SQL Database のコピーの作成"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure ポータルを使用した Azure SQL Database のコピー

> [AZURE.SELECTOR]
- [概要](sql-database-copy.md)
- [Azure ポータル](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

次の手順では、[Azure ポータル](https://portal.azure.com)を使用して、同じサーバーまたは別のサーバーに SQL データベースをコピーする方法を説明します。

SQL データベースをコピーするには、以下の項目が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料評価版**をクリックしてからこの記事に戻り、最後まで完了してください。
- コピーする SQL データベース。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。


## SQL データベースのコピー

コピーするデータベースの SQL データベース ページを開きます。

1.	[Azure ポータル](https://portal.azure.com)にアクセスします。
2.	**[More services (その他のサービス)]**、**[SQL データベース]**、目的のデータベースの順にクリックします。
3.	SQL データベース ページで、**[コピー]** をクリックします。

    ![SQL Database](./media/sql-database-copy-portal/sql-database-copy.png)

1.  **[コピー]** ページには、既定のデータベース名が入力されています。必要に応じて (サーバー上のすべてのデータベースに一意の名前を付ける必要がある場合など) 別の名前を入力します。
2.  **[対象サーバー]** を選択します。ターゲット サーバーとは、データベース コピーが作成される場所です。同じサーバーまたは別のサーバーにデータベースをコピーできます。サーバーを作成したり、一覧から既存のサーバーを選択したりできます。
3.  **[ターゲット サーバー]** を選択すると、**[エラスティック データベース プール]** オプションと **[価格レベル]** オプションが有効になります。サーバーにプールがある場合は、そこにデータベースをコピーできます。
3.  **[OK]** をクリックし、コピー プロセスを開始します。

    ![SQL Database](./media/sql-database-copy-portal/copy-page.png)


## コピー操作の進行状況の監視

- コピーの開始後、ポータル通知をクリックすると、詳細が表示されます。

    ![通知][3]
 
    ![監視][4]


## サーバーにデータベースが存在することの確認

- **[More services (その他のサービス)]**、**[SQL データベース]** の順にクリックし、新しいデータベースが**オンライン**であることを確認します。


## ログインの解決

コピー操作が完了した後にログインを解決するには、[ログインの解決](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)に関するページをご覧ください


## 次のステップ

- Azure SQL Database のコピーの概要については、「[Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。
- PowerShell を使用してデータベースをコピーするには、「[PowerShell を使用した Azure SQL Database のコピー](sql-database-copy-powershell.md)」をご覧ください。
- Transact-SQL を使用してデータベースをコピーするには、「[T-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md)」をご覧ください。
- 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。



## その他のリソース

- [ログインの管理](sql-database-manage-logins.md)
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
- [データベースを BACPAC にエクスポートする](sql-database-export.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0921_2016-->