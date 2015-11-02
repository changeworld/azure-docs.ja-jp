<properties
	pageTitle="Azure SQL Database のコピー"
	description="Azure SQL Database のコピーの作成"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/16/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure SQL Database のコピー

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

次の手順は、[Azure プレビュー ポータル](https://portal.azure.com)を使って SQL データベースをコピーする方法を示しています。データベース コピー操作により新しい SQL データベースが作成されます。コピーは、同じサーバーか別のサーバーで作成するデータベースのスナップショット バックアップです。

> [AZURE.NOTE]Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。

コピー プロセスが完了すると、新しいデータベースは、コピー元のデータベースに依存せずに完全に機能するデータベースになります。コピーの完了時点で、新しいデータベースのトランザクションはコピー元のデータベースと同じになります。データベース コピーのサービス層とパフォーマンス レベル (価格レベル) はコピー元のデータベースと同じになります。コピーの完了後、コピーは完全に機能する独立したデータベースになります。ログイン、ユーザー、アクセス許可は非依存で管理できます。


データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。


SQL Database をコピーするには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- コピーする SQL データベース。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。



## SQL データベースのコピー

コピーするデータベースの SQL データベース ブレードを開きます。

1.	[Azure プレビュー ポータル](https://portal.azure.com)にアクセスします。
2.	[参照] > [SQL データベース] の順に選択し、コピーするデータベースにアクセスします。
3.	SQL データベース ブレードで、**[コピー]** をクリックし、**コピー** ブレードを開きます。

    ![データベースのコピー][1]

1.  データベース コピーの名前を入力します。既定の名前が提示されますが、任意で変更できます。
2.  **[対象サーバー]** を選択します。対象サーバーとは、データベース コピーが作成される場所です。新しいサーバーを作成したり、一覧から既存のサーバーを選択したりできます。
3.  **[OK]** をクリックし、コピー プロセスを開始します。

    ![データベースの名前とサーバー][2]




## コピー操作の進行状況の監視

2.	コピーの開始後、ポータル通知をクリックすると、詳細が表示されます。


    ![通知][3]

 
    ![monitor][4]





## サーバーにデータベースが存在することの確認

- **[参照]** > **[SQL データベース]** の順にクリックし、新しいデータベースが**オンライン**であることを確認します。



## 次のステップ

- [SQL Server Management Studio (SSMS) での接続](sql-database-connect-to-database.md)
- [データベースを BACPAC にエクスポートする](sql-database-export.md)



## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=Oct15_HO4-->