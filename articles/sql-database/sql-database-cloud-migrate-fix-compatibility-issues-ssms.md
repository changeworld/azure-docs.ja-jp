<properties
   pageTitle="SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正する | Microsoft Azure"
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
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正する

> [AZURE.SELECTOR]
- [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)を使用する
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) を使用する
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) を使用する

上級ユーザーは、Azure SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正できます。


> [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。


## SQL Server Management Studio を使用する

SQL Server Management Studio では、**ALTER DATABASE** などの各種 Transact-SQL コマンドを使用して互換性に関する問題を修正できます。この方法は主に、ライブ データベースでの Transact-SQL の操作に慣れている上級ユーザーを対象にしています。それ以外の場合は、SSDT を使用することをお勧めします。



## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->