<properties
	pageTitle="BACPAC ファイルをインポートして新しい Azure SQL Database を作成する"
	description="既存の BACPAC ファイルをインポートして新しい Azure SQL Database を作成する"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/13/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# BACPAC ファイルをインポートして新しい Azure SQL Database を作成する


**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

この記事は、[Azure プレビュー ポータル](https://portal.azure.com)を使用して BACPAC ファイルから新しい Azure SQL Database を作成する手順について説明します。

BACPAC は、データベース スキーマとデータを含む .bacpac ファイルです。詳細については、「[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)」の「バックアップ パッケージ (.bacpac)」を参照してください。

Azure Storage BLOB コンテナーからインポートされた BACPAC からデータベースが作成されます。Azure Storage に .bacpac ファイルがない場合は、「[SQL Database の BACPAC の作成およびエクスポート](sql-database-export.md)」の手順に従って作成できます。


> [AZURE.NOTE]Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。


.bacpac から SQL Database をインポートするには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database V12 サーバー。V12 サーバーがない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- [Azure ストレージ アカウント (クラシック)](storage-create-storage-account.md) の BLOB コンテナーにインポートするデータベースの .bacpac ファイル。


## データベースを含めるサーバーの選択

次のように、インポートするデータベースの [SQL Server] ブレードを開きます。

1.	[Azure プレビュー ポータル](https://portal.azure.com)に移動します。
2.	**[すべて参照]** をクリックします。
3.	**[SQL Server]** をクリックします。
2.	データベースを復元するサーバーをクリックします。
3.	[SQL Server] ブレードで、**[データベースのインポート]** をクリックして、**[データベースのインポート]** ブレードを開きます。

    ![データベースのインポート][1]

1.  **[Storage]** をクリックし、ストレージ アカウント、BLOB コンテナー、および .bacpac ファイルを選択して、**[OK]** をクリックします。

    ![ストレージ オプションの構成][2]

1.  新しいデータベースの価格レベルを選択し、**[選択]**をクリックします。

    ![価格レベルの選択][3]

1.  **[データベース名]** を入力します。
2.  データベースをインポートする Azure SQL Server の **[サーバー管理ログイン]** と **[パスワード]** を入力します。
1.  **[作成]** をクリックして、BACPAC からデータベースを作成します。

    ![create database][4]

**[作成]** をクリックすると、サービスにデータベースのインポート要求が送信されます。データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

## インポート操作の進行状況の監視

2.	**[すべて参照]** をクリックします。
3.	**[SQL Server]** をクリックします。
2.	復元先のサーバーをクリックします。
3.	[SQL Server] ブレードで、**[インポート/エクスポート履歴]** をクリックします。

    ![インポート/エクスポート履歴][5] ![インポート/エクスポート履歴][6]





## サーバーにデータベースが存在することの確認

2.	**[すべて参照]** をクリックします。
3.	**[SQL Database]** をクリックしし、新しいデータベースが**オンライン**であることを確認します。



## 次のステップ

- [SQL Server Management Studio (SSMS) での接続](sql-database-connect-to-database.md)



## その他のリソース

- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=Nov15_HO2-->