<properties
	pageTitle="Azure SQL Database の BACPAC の作成およびエクスポート"
	description="Azure SQL Database の BACPAC の作成およびエクスポート"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/20/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure SQL Database の BACPAC の作成およびエクスポート

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

この記事では、[Azure プレビュー ポータル](https://portal.azure.com)で Azure SQL Database の BACPAC をエクスポートする手順について説明します。

BACPAC は、データベース スキーマとデータを含む .bacpac ファイルです。詳細については、「[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)」の「バックアップ パッケージ (.bacpac)」を参照してください。

> [AZURE.NOTE]Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。


BACPAC は Azure Storage BLOB コンテナーにエクスポートされます。操作が正常に完了した後にダウンロードできます。

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- [Azure ストレージ アカウント](storage-create-storage-account.md) (データベース バックアップを格納する BLOB コンテナーを含む)。現在、ストレージ アカウントではクラシック デプロイ モデルを使用する必要があります。したがって、ストレージ アカウントを作成する際には、**クラシック**を選択してください。 


## データベースのエクスポート

.bacpac ファイルとしてエクスポートするデータベースの [SQL Database] ブレードを開きます。

> [AZURE.IMPORTANT]トランザクションに関する BACPAC ファイルの一貫性を保証するには、最初に[データベースのコピーを作成](sql-database-copy.md)してエクスポートする必要があります。

1.	[Azure プレビュー ポータル](https://portal.azure.com)にアクセスします。
2.	**[すべて参照]** をクリックします。
3.	**[SQL データベース]** をクリックします。
2.	BACPAC としてエクスポートするデータベースをクリックします。
3.	[SQL Database] ブレードで、**[エクスポート]** をクリックして **[データベースのエクスポート]** ブレードを開きます。

    ![[エクスポート] ボタン][1]

1.  **[Storage]** をクリックし、ストレージ アカウントと BACPAC が格納される BLOB コンテナーを選択します。

    ![データベースのエクスポート][2]

1.  バックアップするデータベースを含む Azure SQL Server の **[サーバー管理ログイン]** と **[パスワード]** を入力します。
1.  **[作成]** をクリックしてデータベースをエクスポートします。

**[作成]** をクリックすると、データベースのエクスポート要求が作成され、それがサービスに送信されます。データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

## エクスポート操作の進行状況の監視

2.	**[すべて参照]** をクリックします。
3.	**[SQL Server]** をクリックします。
2.	エクスポートした元 (ソース) のデータベースを含むサーバーをクリックします。
3.	[SQL Server] ブレードで、**[インポート/エクスポート履歴]** をクリックします。

    ![インポート/エクスポート履歴][3] ![インポート/エクスポート履歴][4]

## BACPAC がストレージ コンテナーにあることの確認

2.	**[すべて参照]** をクリックします。
3.	**[ストレージ アカウント (クラシック)]** をクリックします。
2.	BACPAC を格納したストレージ アカウントをクリックします。
3.	**[コンテナー]** をクリックし、データベースをエクスポートしたコンテナーを選択してバックアップの詳細を参照します (ここから BACPAC をダウンロードして保存できます)。

    ![.bacpac ファイルの詳細][5]


## 次のステップ

- [Azure SQL Database のインポート](sql-database-import.md)



## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Oct15_HO4-->