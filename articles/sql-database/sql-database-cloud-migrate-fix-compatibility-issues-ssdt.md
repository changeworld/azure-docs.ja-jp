<properties
   pageTitle="SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する | Microsoft Azure"
   description="Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード、SSDT"
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
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# Visual Studio 用の SQL Server Data Tools を使用して Azure SQL Database に SQL Server データベースを移行する 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

この記事では、Azure SQL Database に移行する前に Visual Studio 用の SQL Server Data Tools を使用して SQL Server データベースの互換性問題を検出し修正する方法について説明します。

## Visual Studio 用の SQL Server Data Tools を使用する

Visual Studio 用の SQL Server Data Tools ("SSDT") を使用すると、データベース スキーマを Visual Studio データベース プロジェクトにインポートして分析できます。分析するには、SQL Database V12 にプロジェクトのターゲット プラットフォームを指定し、プロジェクトを構築します。構築に成功した場合は、データベースには互換性があります。構築に失敗した場合、SSDT (またはこのトピックで説明したその他のツールのいずれか) を使用してエラーを解決できます。プロジェクトが正常に構築されると、それをソース データベースのコピーとして発行できます。次に、SSDT のデータ比較機能を使用して、ソース データベースから Azure SQL V12 互換のデータベースにデータをコピーできます。その後、更新されたこのデータベースを移行できます。このオプションを使用するには、[最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx) をダウンロードします。

  ![VSSSDT の移行ダイアグラム](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] スキーマのみの移行が必要な場合は、Visual Studio から Azure SQL Database に直接、発行することができます。この方法は移行ウィザードのみで処理できる変更よりも多数の変更を、データベース スキーマで行う必要がある場合に使用します。

## Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を検出する
   
1.	Visual Studio で **SQL Server オブジェクト エクスプローラー**を開きます。**[SQL Server の追加]** を使用して、移行されるデータベースを含む SQL Server インスタンスに接続します。エクスプローラーでデータベースを選択して右クリックし、**[新しいプロジェクトの作成]** を選択します。
    
	![新しいプロジェクト](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
   
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
    
## Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を修正する        
    
1.	最初のスクリプトをダブルクリックしてクエリ ウィンドウでスクリプトを開き、スクリプトをコメント アウトし、スクリプトを実行します。![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.	このプロセスを互換性のない各スクリプトに対して、エラーがなくなるまで繰り返します。

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.	データベースのエラーがなくなったら、プロジェクトを右クリックし、**[発行]** を選択します。これにより、データベースがビルドされ、ソース データベースのコピーに発行されます (少なくとも最初のうちは、コピーを使用することを強くお勧めします)。
 - 発行前に、ソースの SQL Server のバージョン (SQL Server 2014 より前) に応じて、プロジェクトのターゲット プラットフォームをリセットしてデプロイメントを有効にすることが必要になる場合があります。
 - 古い SQL Server データベースを移行する場合は、データベースを新しいバージョンの SQL Server に移行するまで、ソースの SQL Server でサポートされていない機能をプロジェクトに導入しないでください。

    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
    
4.	SQL Server オブジェクト エクスプ ローラーで、ソース データベースを右クリックして **[データ比較]** をクリックし、プロジェクトを元のデータベースと比較して、ウィザードによってどのような変更が加えられたかを把握します。Azure SQL V12 バージョンのデータベースを選択し、**[完了]** をクリックします。
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
    
5.	検出された相違点を確認し、**[Update Target]** をクリックしてデータをソース データベースから Azure SQL V12 データベースに移行します。
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
    
6.	デプロイメント方法を選択します。[互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md)に関する記事をご覧ください。

## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->