<properties
	pageTitle="SQL Database チュートリアル: SQL データベースの作成 | Microsoft Azure"
	description="SQL Database 論理サーバー、サーバー ファイアウォール規則、SQL データベース、サンプル データを設定する方法について説明します。また、クライアント ツールによる接続方法、ユーザーの構成方法、データベース ファイアウォール規則の設定方法について説明します。"
	keywords="SQL データベース チュートリアル, SQL データベースの作成"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# SQL Database チュートリアル: Azure Portal を使用して数分で SQL データベースを作成する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

このチュートリアルでは、Azure ポータルを使用して次のことを行う方法を説明します。

- SQL データベースをホストするために、Azure SQL Database 論理サーバーを作成する。
- SQL データベースを作成する (データなし、サンプル データ使用、SQL データベース バックアップのデータ使用)。
- 単一の IP アドレスまたは IP アドレスの範囲に対してサービスレベルのファイアウォール規則を作成します。

これらのタスクは、[C#](sql-database-get-started-csharp.md) または [PowerShell](sql-database-get-started-powershell.md) を使用して実行することができます。

[AZURE.INCLUDE [ログイン](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [SQL Database 論理サーバーを作成する](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 次のステップ
ここでは、この SQL Database チュートリアルを完了し、いくつかのサンプル データを使用してデータベースを作成しました。次はお気に入りのツールを使用して探索します。

- Transact-SQL と SQL Server Management Studio (SSMS) に慣れている場合は、[SSMS を使用した SQL データベースの接続とクエリ](sql-database-connect-query-ssms.md)の方法に関するページを参照してください。

- Excel に詳しい場合は、[Excel を使用した Azure SQL データベースへの接続](sql-database-connect-excel.md)方法を参照してください。

- コーディングを開始する準備ができている場合、「[SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」でプログラミング言語を選択します。

- オンプレミスの SQL Server データベースを Azure に移動する場合の詳細については、[SQL Database へのデータベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。

- BCP コマンド ライン ツールを使用して CSV ファイルから新しいテーブルにデータを読み込む場合は、[BCP を使用した CSV ファイルから SQL Database へのデータの読み込み](sql-database-load-from-csv-with-bcp.md)に関するページを参照してください。


## その他のリソース

[SQL Database とは](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->