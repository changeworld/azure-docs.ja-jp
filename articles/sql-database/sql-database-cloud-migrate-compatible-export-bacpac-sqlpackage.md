---
title: "SqlPackage: SQL Server データベースを BACPAC ファイルにエクスポートする (Azure) | Microsoft Docs"
description: "この記事では、SqlPackage コマンドライン ユーティリティを使用して SQL Server データベースを BACPAC ファイルにエクスポートする方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: e38c42ea1c078f9f2e11ae51be37d66671392828


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>SqlPackage を使用して SQL Server データベースを BACPAC ファイルにエクスポートする
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。 このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

1. コマンド プロンプトを開き、sqlpackage.exe コマンドライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio と SQL Server の両方に付属します。 コンピュータの検索機能を使用してご利用の環境のパスを検索します。
2. 次の sqlpackage.exe コマンドをご利用の環境に合わせた引数で実行します。
   
```    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps
* [Newest version of SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Import a BACPAC to Azure SQL Database using SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Import a BACPAC to Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Import a BACPAC to Azure SQL Database Azure portal](sql-database-import.md)
* [Import a BACPAC to Azure SQL Database PowerShell](sql-database-import-powershell.md)

## Additional resources
* [SQL Database features](sql-database-features.md)
* [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


