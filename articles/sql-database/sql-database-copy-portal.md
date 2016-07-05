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
	ms.date="06/16/2016"
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

次の手順では、[Azure ポータル](https://portal.azure.com) を使用して、同じサーバーまたは別のサーバーに SQL データベースをコピーする方法を説明します。

SQL Database をコピーするには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- コピーする SQL データベース。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。


## SQL データベースのコピー

コピーするデータベースの SQL データベース ブレードを開きます。

1.	[Azure ポータル](https://portal.azure.com)にアクセスします。
2.	[参照] > [SQL データベース] の順に選択し、コピーするデータベースにアクセスします。
3.	SQL データベース ブレードで、**[コピー]** をクリックし、**コピー** ブレードを開きます。

    ![データベースのコピー][1]

1.  データベース コピーの名前を入力します。既定の名前が提示されますが、任意で変更できます。
2.  **[対象サーバー]** を選択します。対象サーバーとは、データベース コピーが作成される場所です。新しいサーバーを作成したり、一覧から既存のサーバーを選択したりできます。
3.  **[OK]** をクリックし、コピー プロセスを開始します。

    ![データベースの名前とサーバー][2]


## コピー操作の進行状況の監視

- コピーの開始後、ポータル通知をクリックすると、詳細が表示されます。

    ![通知][3]
 
    ![監視][4]


## サーバーにデータベースが存在することの確認

- **[参照]** > **[SQL データベース]** の順にクリックし、新しいデータベースが**オンライン**であることを確認します。


## ログインの解決

コピー操作が完了した後にログインを解決するには、[ログインの解決](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)に関するページをご覧ください。


## 次のステップ

- Azure SQL Database のコピーの概要については、「[Azure SQL Database のコピー](sql-database-copy.md)」をご覧ください。
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

<!---HONumber=AcomDC_0622_2016-->