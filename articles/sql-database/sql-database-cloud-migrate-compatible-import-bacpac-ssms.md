<properties
   pageTitle="SQL Server データベースの Azure SQL Database への移行"
   description="Microsoft Azure SQL Database、データベースのデプロイ、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード"
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
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# SSMS を使用して BACPAC から SQL Database にインポートする

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルから SQL Database にインポートする方法について説明します。

> [AZURE.NOTE]以下の手順では、Azure SQL 論理インスタンスを既にプロビジョニングしてあり、接続情報がわかっているものとします。

1. 最新バージョンの SQL Server Management Studio があることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

	 >[AZURE.IMPORTANT]常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. BACPAC が作成されたら、Azure SQL Database サーバーに接続し、**[データベース]** フォルダーを右クリックして、**[データ層アプリケーションのインポート...]** をクリックします。

    ![[データ層アプリケーションのインポート] メニュー項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	インポート ウィザードで、エクスポートした BACPAC ファイルを選択して、Azure SQL Database に新しいデータベースを作成します。

    ![設定のインポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Azure SQL DB でのデータベースの **[新しいデータベース名]** を指定し、**[Microsoft Azure SQL Database のエディション]** (サービス階層)、**[データベースの最大サイズ]**、および **[サービスの目的]** (パフォーマンス レベル) を設定します。

    ![データベース設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	**[次へ]**、**[完了]** の順にクリックし、Azure SQL Database サーバーの新しいデータベースに BACPAC ファイルをインポートします。

7. オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。

8.	Azure ポータルを使用して、データベースとそのプロパティを表示します。

<!---HONumber=AcomDC_1223_2015-->