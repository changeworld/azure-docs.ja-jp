---
title: "SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正する | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 355353fb15a00860573699cc652543b61c62c2c1


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正する
> [!div class="op_single_selector"]
> *  [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)
> *  [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> *  [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

上級ユーザーは、Azure SQL Database へ移行する前に SQL Server Management Studio を使用して SQL Server データベースの互換性に関する問題を修正できます。

> [!IMPORTANT]
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio を使用する
SQL Server Management Studio では、 **ALTER DATABASE**などの各種 Transact-SQL コマンドを使用して互換性に関する問題を修正できます。 この方法は主に、ライブ データベースでの Transact-SQL の操作に慣れている上級ユーザーを対象にしています。 それ以外の場合は、SSDT を使用することをお勧めします。 

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


