---
title: "SQLPackage: Azure SQL Server データベースの互換性 | Microsoft Docs"
description: "この記事では、SqlPackage を使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します"
keywords: "Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6728693290e2c1d6c970c60c7d8ef674764e053c
ms.openlocfilehash: 834dae604fa87f753e8d050ce7cdfa7e9613f462


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>SqlPackage.exe を使用して SQL Database の互換性を判定する
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

この記事では、 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します。

## <a name="using-sqlpackageexe"></a>SqlPackage.exe を使用する
1. コマンド プロンプトを開き、sqlpackage.exe の最新バージョンを含むディレクトリに変更します。 このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

2. 次の SqlPackage コマンドをご利用の環境に合わせた引数で実行します。

   ```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
   ```   

   | 引数 | 説明 |
   | --- | --- |
   | <server_name> |ソース サーバー名 |
   | <database_name> |移行元データベースの名前 |
   | <target_file> |BACPAC ファイルのファイル名と場所 |
   | <schema_name.table_name> |データをターゲット ファイルに出力するテーブル |
   | <output_file> |エラーがある場合、エラーのある出力ファイルのファイル名と場所 |
   
    /p:TableData 引数を指定するのは、すべてのテーブルからデータをエクスポートするのではなく、Azure SQL DB V12 にエクスポートするためのデータベースの互換性をテストするだけであるためです。 残念ながら、sqlpackage.exe の export 引数はゼロ テーブルの抽出をサポートしていません。 1 つの小さなテーブルなど少なくとも&1; つのテーブルを指定する必要があります。 < output_file > には、すべてのエラーのレポートが含まれます。 「2>&1」文字列は、標準出力とコマンド実行の結果として生成された標準エラーの両方を指定の出力ファイルにパイプ処理します。
   
    ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 出力ファイルを開き、互換性エラーがある場合は確認します。 
   
    ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [データベース移行に関する互換性の問題の修正](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO2-->


