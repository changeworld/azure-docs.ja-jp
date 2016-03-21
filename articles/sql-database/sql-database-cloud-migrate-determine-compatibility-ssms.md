<properties
   pageTitle="SSMS を使用して SQL Database の互換性を判定する"
   description="Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、[データ層アプリケーションのエクスポート] ウィザード"
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

# SSMS を使用して SQL Database の互換性を判定する

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
この記事では、SQL Server Management Studio の [データ層アプリケーションのエクスポート] ウィザードを使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します。

## SQL Server Management Studio を使用する

1. 最新バージョンの SQL Server Management Studio があることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

 	 >[AZURE.IMPORTANT]常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。
3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. エクスポート ウィザードで **[次へ]** をクリックし、**[設定]** タブで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。BACPAC ファイルは、データベースの互換性に問題がない場合にのみ保存されます。互換性の問題がある場合は、コンソールに表示されます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. **[詳細設定]** タブをクリックし、**[すべて選択]** チェック ボックスをオフにしてデータのエクスポートをスキップします。ここでの目的は、互換性をテストすることだけです。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. **[次へ]** をクリックし、**[完了]** をクリックします。データベースの互換性に問題がある場合は、ウィザードによるスキーマの検証後に表示されます。

	![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. エラーが表示されない場合は、データベースは互換性があり、移行できる状態です。エラーがある場合は、修正する必要があります。エラーを表示するには、**[スキーマの検証]** の **[エラー]** をクリックします。![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	*.BACPAC ファイルが生成されたら、お使いのデータベースは SQL Database と互換性があるので移行できます。

## 次のステップ: 互換性の問題の修正

必要に応じて、[データベースの互換性の問題](sql-database-cloud-migrate-fix-compatibility-issues.md)を修正します。

<!---HONumber=AcomDC_1223_2015-->