<properties
	pageTitle="SQL Database チュートリアル: SQL データベースの作成 | Microsoft Azure"
	description="SQL Database 論理サーバー、サーバー ファイアウォール規則、SQL Database、サンプル データを設定し、クライアント ツールに接続し、ユーザーとデータベースとファイアウォール規則を構成する方法について説明します。"
	keywords="SQL データベース チュートリアル, SQL データベースの作成"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="carlrab"/>

# SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

このチュートリアルでは、Azure ポータルの使用方法について説明します。

- SQL Database をホストする SQL Database 論理サーバーを作成する
- データのソースを選んで SQL データベースを作成する (データなし、サンプル データ使用、SQL データベース バックアップ データ使用)。
- 単一の IP アドレスまたは IP アドレスの範囲に対してサービスレベルのファイアウォール規則を作成します。

同じタスクを [C#](sql-database-get-started-csharp.md) または [PowerShell](sql-database-get-started-powershell.md) を使用して実行するには、次のリンク先を参照してください。

[AZURE.INCLUDE [ログイン](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SQL Database 論理サーバーを作成する](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 次のステップ
ここでは、この SQL Database チュートリアルを完了し、いくつかのサンプル データを使用してデータベースを作成しました。次はお気に入りのツールを使用して調査します。

- Transact-SQL と SQL Server Management Studio に慣れている場合は、[SSMS を使用した SQL データベースの接続とクエリ](sql-database-connect-query-ssms.md)の方法に関するページを参照してください。

- Excel に詳しい場合は、[Excel を使用した SQL Database への接続](sql-database-connect-excel.md)方法を参照してください。

- コーディングを開始する準備が整ったら、「[SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」でプログラミング言語を選択します。

- オンプレミスの SQL Server データベースを Azure に移動する場合の詳細については、[Azure SQL Database へのデータベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。


## その他のリソース

[SQL Database の概要](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0504_2016-->