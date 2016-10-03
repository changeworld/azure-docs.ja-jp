<properties
   pageTitle="SqlPackage を使用して BACPAC ファイルから SQL Database にインポートする"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのインポート、BACPAC ファイルのインポート、sqlpackage"
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

# SqlPackage を使用して BACPAC ファイルから SQL Database にインポートする

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure ポータル](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルから SQL Database にインポートする方法について説明します。このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/ja-JP/download/details.aspx?id=53876) を直接ダウンロードすることもできます。


> [AZURE.NOTE] 以下の手順では、SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっていて、移行元データベースに互換性があると検証済みであることを前提にしています。

## SqlPackage を使用して BACPAC ファイルを Azure SQL Database にインポートする

以下の手順では、[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) コマンド ライン ユーティリティを使用して、互換性のある SQL Server データベース (または Azure SQL Database) を BACPAC ファイルからインポートします。

> [AZURE.NOTE] 以下の手順では、Azure SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっていることを前提にしています。

1. コマンド プロンプトを開き、sqlpackage.exe コマンドライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio と SQL Server の両方に付属します。
2. 次の sqlpackage.exe コマンドをご利用の環境に合わせた引数で実行します。

	`sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

	| 引数 | 説明 |
	|---|---|
	| < server\_name > | ターゲット サーバー名 |
	| < database\_name > | ターゲット データベース名 |
	| < user\_name > | ターゲット サーバーのユーザー名 |
	| < password > | ユーザーのパスワード |
	| < source\_file > | インポートする BACPAC ファイルのファイル名と場所 |

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## 次のステップ

- [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## その他のリソース

- [SQL Database V12](sql-database-v12-whats-new.md)
- [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->