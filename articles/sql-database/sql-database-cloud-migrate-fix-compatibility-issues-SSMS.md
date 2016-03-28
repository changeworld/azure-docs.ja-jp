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

## SQL Server Management Studio を使用する

SQL Server Management Studio では、**ALTER DATABASE** などの各種 Transact-SQL コマンドを使用して互換性に関する問題を修正できます。

## 次のステップ: 移行方法を選択して移行を実行する

[移行方法を選択](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)します。

<!---HONumber=AcomDC_0316_2016-->