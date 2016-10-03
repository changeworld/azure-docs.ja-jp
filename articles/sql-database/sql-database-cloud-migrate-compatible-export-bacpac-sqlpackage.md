<properties
   pageTitle="SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする | Microsoft Azure"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、sqlpackage"
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

# SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/ja-JP/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

1. コマンド プロンプトを開き、sqlpackage.exe コマンドライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio と SQL Server の両方に付属します。コンピュータの検索機能を使用してご利用の環境のパスを検索します。
2. 次の sqlpackage.exe コマンドをご利用の環境に合わせた引数で実行します。

	'sqlpackage.exe /Action:Export /ssn:< server\_name > /sdn:< database\_name > /tf:< target\_file >

	| 引数 | 説明 |
	|---|---|
	| <server\_name> | ソース サーバー名 |
	| <database\_name> | 移行元データベースの名前 |
	| <target\_file> | BACPAC ファイルのファイル名と場所 |

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [SSMS を使用した Azure SQL Database への BACPAC のインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage を使用した Azure SQL Database への BACPAC のインポート](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure ポータルを使用した Azure SQL Database への BACPAC のインポート](sql-database-import.md)
- [PowerShell を使用した Azure SQL Database への BACPAC のインポート](sql-database-import-powershell.md)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->