---
title: "Azure SQL Database へ移行する前にSQL Server Management Studio を使用して SQL Database の互換性を判定する | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、SQL Database の互換性、[データ層アプリケーションのエクスポート] ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/29/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 15d0bf86d6094b6a8c0d6aaf1e1bb59382a81ffb


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Azure SQL Database へ移行する前にSQL Server Management Studio を使用して SQL Database の互換性を判定する
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

この記事では、SQL Server Management Studio の [データ層アプリケーションのエクスポート] ウィザードを使用して、SQL Server データベースを SQL Database に移行するための互換性のあるかどうかを判定します。

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio を使用する
1. 最新バージョンの SQL Server Management Studio があることを確認します。 Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。
   
   > [!IMPORTANT]
   > 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。
3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。
   
    ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. エクスポート ウィザードで **[次へ]** をクリックし、**[設定]** タブで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。 BACPAC ファイルは、データベースの互換性に問題がない場合にのみ保存されます。 互換性の問題がある場合は、コンソールに表示されます。
   
    ![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. データのエクスポートをスキップするために、**[詳細設定]** タブをクリックして **[すべて選択]** チェック ボックスをオフにします。 ここでの目的は、互換性をテストすることだけです。
   
    ![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. **[次へ]** をクリックし、**[完了]** をクリックします。 データベースの互換性に問題がある場合は、ウィザードによるスキーマの検証後に表示されます。
   
    ![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. エラーが表示されない場合は、データベースは互換性があり、移行できる状態です。 エラーがある場合は、修正する必要があります。 エラーを表示するには、**[スキーマの検証]** の **[エラー]** をクリックします。 
    ![設定のエクスポート](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. *.BACPAC ファイルが生成されたら、お使いのデータベースは SQL Database と互換性があるので移行できます。

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [データベース移行に関する互換性の問題の修正](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


