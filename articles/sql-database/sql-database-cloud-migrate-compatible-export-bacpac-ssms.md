---
title: "SQL Server Management Studio を使用して SQL Server データベースを BACPAC ファイルにエクスポートする | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、データ層アプリケーションのエクスポート ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: f6f64479f6ddfda4badb8e826e03369c476d6d1d


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>SQL Server Management Studio を使用して SQL Server データベースを BACPAC ファイルにエクスポートする
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。 

1. 最新バージョンの SQL Server Management Studio があることを確認します。 Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。
   
   > [!IMPORTANT]
   > 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。
   
    ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。
   
    ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. エクスポート ウィザードで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。 エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。 一部またはすべてのテーブルからデータを除外する場合は、[詳細設定] タブを使用してください。 たとえば、すべてのテーブルではなく参照テーブルのデータのみをエクスポートするように選択できます。

    ![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > BACPAC を Azure Blob Storage にエクスポートする場合は、Standard Storage を使用します。 Premium Storage からの BACPAC のインポートはサポートされていません。
   >
   
## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [SSMS を使用した Azure SQL Database への BACPAC のインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [SqlPackage を使用した Azure SQL Database への BACPAC のインポート](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Azure ポータルを使用した Azure SQL Database への BACPAC のインポート](sql-database-import.md)
* [PowerShell を使用した Azure SQL Database への BACPAC のインポート](sql-database-import-powershell.md)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


