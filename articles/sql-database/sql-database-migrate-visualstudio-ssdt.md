<properties 
   pageTitle="Visual Studio と SSDT を使用した移行"
	description="Microsoft Azure SQL Database、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#データベースのインプレース更新と Azure SQL Database へのデプロイ

![alt text](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Azure SQL Database V12 へのオンプレミスのデータベースの移行で、Azure SQL Database ではサポートされていない SQL Server 機能をデータベースが使用しているためスキーマの変更を実行する必要がある場合、またはオンプレミスのデータベース内にサポートされていない機能があるかどうかをテストする場合はこのオプションを使用します。

[最新の Visual Studio 用 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) は Visual Studio 2013 Update 4 以降で使用してください。

このオプションでは、

 - まず Visual Studio 用の SQL Server Data Tools ("SSDT") を使用して、ソース データベースからデータベース プロジェクトを作成します。 
 - その後、プロジェクトのターゲット プラットフォームを Azure SQL Database V12 に設定し、プロジェクトをビルドして互換性のすべての問題を特定します。 
 - プロジェクトのビルドに成功すると、スキーマは、ソース データベースのコピーに発行されます (ソース データベースは上書きされません)。
 - SSDT のデータ比較機能を使用してソース データベースと新しく作成した Azure SQL 互換のデータベースを比較し、ソース データベースのデータを使用して新しいデータベースを更新します。 
 - その後、更新されたデータベースは、SSMS を使用して、直接または BACPAC ファイルのエクスポートとインポートによって Azure にデプロイされます。
 
>[AZURE.NOTE]注: スキーマのみのデプロイメントが必要な場合は、更新されたスキーマを Visual Studio から Azure SQL Database に直接発行することができます。

## 移行の手順

1.	Visual Studio で **SQL Server オブジェクト エクスプローラー**を開きます。**[SQL Server の追加]** を使用して、移行されるデータベースを含む SQL Server インスタンスに接続します。エクスプローラーでデータベースを選択して右クリックし、**[新しいプロジェクトの作成]** を選択します。 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	インポート設定を構成し、**[アプリケーション スコープのオブジェクトのみをインポートする]** チェック ボックスをオンにします。参照先のログイン、アクセス許可、データベースの設定をインポートするためのオプションの選択を解除します。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	**[開始]** をクリックしてデータベースをインポートし、プロジェクトを作成します。これには、データベース内の各オブジェクトの T-SQL スクリプト ファイルが含まれます。スクリプト ファイルは、プロジェクト内のフォルダーに入れ子になっています。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Visual Studio のソリューション エクスプローラーで、データベース プロジェクトを右クリックして [プロパティ] を選択します。これにより、**[プロジェクトの設定]** ページが開きます。このページでは、ターゲット プラットフォームを Microsoft Azure SQL Database V12 に構成します。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	プロジェクトを右クリックして **[ビルド]** を選択し、プロジェクトをビルドします。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	**[エラー一覧]** には、各非互換性が表示されます。この場合、ユーザー名 NT AUTHORITY\\NETWORK SERVICE は互換性がありません。互換性がないため、これをコメント アウトまたは削除できます (このログインとロールをデータベース ソリューションから削除することによる影響に対応できます)。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	最初のスクリプトをダブルクリックしてクエリ ウィンドウでスクリプトを開き、スクリプトをコメント アウトし、スクリプトを実行します。![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	このプロセスを互換性のない各スクリプトに対して、エラーがなくなるまで繰り返します。![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	データベースにエラーがなくなったら、プロジェクトを右クリックして **[発行]** を選択し、データベースをビルドしてソース データベースのコピーに発行します (少なくとも最初のうちは、コピーを使用することを強くお勧めします)。 
 - 発行前に、ソースの SQL Server のバージョン (SQL Server 2014 より前) に応じて、プロジェクトのターゲット プラットフォームをリセットしてデプロイメントを有効にすることが必要になる場合があります。 
 - 古い SQL Server データベースを移行する場合は、最初にデータベースを新しいバージョンの SQL Server に移行しない限り、ソースの SQL Server でサポートされていない機能をプロジェクトに導入しないでください。 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	SQL Server オブジェクト エクスプ ローラーで、ソース データベースを右クリックして **[データ比較]** をクリックし、プロジェクトを元のデータベースと比較して、ウィザードによってどのような変更が加えられたかを把握します。Azure SQL V12 バージョンのデータベースを選択し、**[完了]** をクリックします。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	検出された相違点を確認し、**[Update Target]** をクリックしてデータをソース データベースから Azure SQL V12 データベースに移行します。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	SSMS を使用して、Azure SQL V12 互換のデータベース スキーマとデータを Azure SQL Database にデプロイします。「[SSMS を使用した互換性のあるデータベースの移行](sql-database-migrate-ssms.md)」をご覧ください。

<!---HONumber=August15_HO9-->