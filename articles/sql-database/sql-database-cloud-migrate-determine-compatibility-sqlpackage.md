<properties
   pageTitle="SqlPackage.exe を使用して SQL Database の互換性を判定する | Microsoft Azure"
   description="Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、SqlPackage"
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

# SqlPackage.exe を使用して SQL Database の互換性を判定する

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します。

## SqlPackage.exe を使用する

1. コマンド プロンプトを開き、sqlpackage.exe の最新バージョンを含むディレクトリに変更します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。[最新の Visual Studio 用 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) をダウンロードして、最新バージョンの SqlPackage ユーティリティを取得します。
2. 次の SqlPackage コマンドをご利用の環境に合わせた引数で実行します。

	'sqlpackage.exe /Action:Export /ssn:< server\_name > /sdn:< database\_name > /tf:< target\_file > /p:TableData=< schema\_name.table\_name > > < output\_file > 2>&1'

	| 引数 | 説明 |
	|---|---|
	| < server\_name > | ソース サーバー名 |
	| < database\_name > | 移行元データベースの名前 |
	| < target\_file > | BACPAC ファイルのファイル名と場所 |
	| <schema\_name.table\_name> | データをターゲット ファイルに出力するテーブル |
	| < output\_file > | エラーがある場合、エラーのある出力ファイルのファイル名と場所 |

	/p:TableName 引数を指定するのは、すべてのテーブルからデータをエクスポートするのではなく、Azure SQL DB V12 にエクスポートするためのデータベースの互換性をテストするだけであるためです。残念ながら、sqlpackage.exe の export 引数はゼロ テーブルの抽出をサポートしていません。1 つの小さなテーブルなど少なくとも 1 つのテーブルを指定する必要があります。< output\_file > には、すべてのエラーのレポートが含まれます。「> 2>&1」文字列は、標準出力とコマンド実行の結果として生成された標準エラーの両方を指定の出力ファイルにパイプ処理します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 出力ファイルを開き、互換性エラーがある場合は確認します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [データベース移行に関する互換性の問題の修正](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->