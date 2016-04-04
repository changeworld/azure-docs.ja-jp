<properties
   pageTitle="SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する"
   description="Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する

移行元の SQL Server データベースに互換性がないことがわかった場合は、いくつかのオプションを使用して、データベース互換性の問題を修正します。

> [AZURE.SELECTOR]
- [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)を使用する
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) の使用
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) の使用

## SQL Azure 移行ウィザードを使用する

[SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)の CodePlex ツールを使うと、互換性のない移行元データベースから T-SQL スクリプトを生成した後、SQL Database に対応するようにこのスクリプトを変換し、Azure SQL Database に接続してこのスクリプトを実行できます。このツールは、トレース ファイルを分析して互換性の問題の特定も行います。スキーマのみを含むスクリプトを生成することも、BCP 形式のデータを含むスクリプトを生成することもできます。その他のドキュメントには詳細な手順などが含まれ、CodePlex の [SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)に関するページで入手できます。

 ![SAMW の移行ダイアグラム](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] ウィザードで検出できるすべての非互換スキーマを組み込み変換で修正できるとは限りません。処理できない非互換スクリプトは、生成されたスクリプトにコメントが挿入され、エラーとして報告されます。多くのエラーが検出された場合は、Visual Studio または SQL Server Management Studio を使用して、SQL Server 移行ウィザードでは修正できない各エラーを修正します。

## 次のステップ: 移行方法を選択して移行を実行する

[移行方法を選択](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)します。

<!---HONumber=AcomDC_0316_2016-->