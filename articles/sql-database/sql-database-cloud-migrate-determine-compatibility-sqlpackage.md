<properties
   pageTitle="SqlPackage.exe を使用して SQL Database の互換性を判定する"
   description="Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、SqlPackage"
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

# SqlPackage.exe を使用して SQL Database の互換性を判定する

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します。

## SqlPackage.exe を使用する

1. コマンド プロンプトを開き、sqlpackage.exe の最新バージョンを含むディレクトリに変更します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。[最新の Visual Studio 用 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) をダウンロードして、最新バージョンの SqlPackage ユーティリティを取得します。
2. 次の SqlPackage コマンドをご利用の環境に合わせた引数で実行します。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| 引数 | 説明 |
	|---|---|
	| < server_name > | ソース サーバー名 |
	| < database_name > | 移行元データベースの名前 |
	| < target_file > | BACPAC ファイルのファイル名と場所 |
	| < schema_name.table_name > | ターゲット ファイルに出力するデータのテーブル |
	| < output_file > | エラーがある場合、エラーのある出力ファイルのファイル名と場所 |

	/p:TableName 引数を指定するのは、すべてのテーブルからデータをエクスポートするのではなく、Azure SQL DB V12 にエクスポートするためのデータベースの互換性をテストするだけであるためです。残念ながら、sqlpackage.exe の export 引数ではテーブルを抽出しないように指定することはできないので、小さいテーブルを 1 つ指定する必要があります。< output_file > には、すべてのエラーのレポートが含まれます。「> 2>&1」文字列は、標準出力とコマンド実行の結果として生成された標準エラーの両方を指定の出力ファイルにパイプ処理します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 出力ファイルを開き、互換性エラーがある場合は確認します。

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 次のステップ: 互換性の問題の修正

必要に応じて、[データベースの互換性の問題](sql-database-cloud-migrate-fix-compatibility-issues.md)を修正します。

<!---HONumber=AcomDC_1223_2015-->