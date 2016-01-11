<properties
   pageTitle="SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする"
   description="Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、sqlpackage"
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

# SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用して SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。SQL Server Data Tools の最新版を[ダウンロード](https://msdn.microsoft.com/library/mt204009.aspx)し、このユーティリティを入手することもできます。

1. コマンド プロンプトを開き、sqlpackage.exe コマンド ライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio および SQL Server に付属します。コンピュータの検索機能を使用してご利用の環境のパスを検索します。
2. 次の sqlpackage.exe コマンドをご利用の環境に合わせた引数で実行します。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| 引数 | 説明 |
	|---|---|
	| < server_name > | ソース サーバー名 |
	| < database_name > | 移行元データベースの名前 |
	| < target_file > | BACPAC ファイルのファイル名と場所 |

	![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 次のステップ: BACPAC ファイルから SQL Database にインポートする

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure ポータル](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->