
<properties
   pageTitle="SSMS を使用して SQL Server データベースを BACPAC ファイルにエクスポートする"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、データ層アプリケーションのエクスポート ウィザード"
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

# SSMS を使用して SQL Server データベースを BACPAC ファイルにエクスポートする

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。

1. 最新バージョンの SQL Server Management Studio があることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

	 >[AZURE.IMPORTANT]常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. エクスポート ウィザードで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。一部またはすべてのテーブルからデータを除外する場合は、[詳細設定] タブを使用してください。たとえば、すべてのテーブルではなく参照テーブルのデータのみをエクスポートするように選択できます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## 次のステップ: BACPAC ファイルから SQL Database にインポートする

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure ポータル](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->