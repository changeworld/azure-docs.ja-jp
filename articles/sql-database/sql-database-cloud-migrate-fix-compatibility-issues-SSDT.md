<properties
   pageTitle="SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する"
   description="Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード、SSDT"
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
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md) の使用

## Visual Studio 用の SQL Server Data Tools を使用する

Visual Studio 用の SQL Server Data Tools ("SSDT") を使用すると、データベース スキーマを Visual Studio データベース プロジェクトにインポートして分析できます。分析するには、SQL Database V12 にプロジェクトのターゲット プラットフォームを指定し、プロジェクトを構築します。構築に成功した場合は、データベースには互換性があります。構築に失敗した場合、SSDT (またはこのトピックで説明したその他のツールのいずれか) を使用してエラーを解決できます。プロジェクトが正常に構築されたら、これをソース データベースのコピーとして発行し、SSDT のデータ比較機能を使用して、ソース データベースから Azure SQL V12 互換のデータベースにデータをコピーできます。その後、更新されたこのデータベースを移行できます。このオプションを使用するには、[最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx) をダウンロードします。

  ![VSSSDT の移行ダイアグラム](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] スキーマのみの移行が必要な場合は、Visual Studio から Azure SQL Database に直接、発行することができます。この方法は移行ウィザードのみで処理できる変更よりも多数の変更を、データベース スキーマで行う必要がある場合に使用します。

## 次のステップ: 移行方法を選択して移行を実行する

[移行方法を選択](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)します。

<!---HONumber=AcomDC_0316_2016-->