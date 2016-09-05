<properties
   pageTitle="SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する | Microsoft Azure"
   description="Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード"
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

# Azure SQL Database へ移行する前に SQL Azure 移行ウィザードを使用して SQL Server データベースの互換性に関する問題を修正する

> [AZURE.SELECTOR]
- [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)を使用する
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) を使用する
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) を使用する

この記事では、Azure SQL Database へ移行する前に SQL Azure 移行ウィザードを使用して SQL Server データベースの互換性に関する問題を検出し修正する方法について説明します。

## SQL Azure 移行ウィザードを使用する

[SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/) CodePlex ツールを使用して、互換性のないソース データベースから T-SQL スクリプトを生成します。これにより、このスクリプトは、ウィザードによって、SQL Database と互換性があるように変換されます。次に、Azure SQL Database に接続して、スクリプトを実行します。このツールは、トレース ファイルを分析して互換性の問題も特定します。スキーマのみを含むスクリプトを生成することも、BCP 形式のデータを含むスクリプトを生成することもできます。その他のドキュメントには詳細な手順などが含まれ、CodePlex の [SQL Azure 移行ウィザード](http://sqlazuremw.codeplex.com/)に関するページで入手できます。

 ![SAMW の移行ダイアグラム](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] ウィザードで検出できるすべての非互換スキーマを組み込み変換で修正できるとは限りません。処理できない非互換スクリプトは、生成されたスクリプトにコメントが挿入され、エラーとして報告されます。多くのエラーが検出された場合は、Visual Studio または SQL Server Management Studio を使用して、SQL Server 移行ウィザードでは修正できない各エラーを修正します。

## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->