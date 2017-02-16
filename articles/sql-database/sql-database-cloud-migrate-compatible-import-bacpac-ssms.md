---
title: "SSMS: BACPAC ファイルから Azure SQL Database にインポートする | Microsoft Docs"
description: "この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して BACPAC ファイルから SQL Database にインポートする方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースのデプロイ、データベースの移行、データベースのインポート、データベースのエクスポート、移行ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 24375fc6-c94c-43ef-97ec-fce77343b581
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
ms.openlocfilehash: b5a6d90069e87d6115a3b80711f06ccf286b3e88


---
# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>SSMS を使用して BACPAC から SQL Database にインポートする
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Azure ポータル](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルから SQL Database にインポートする方法について説明します。

> [!NOTE]
> 以下の手順では、Azure SQL 論理インスタンスを既にプロビジョニングしてあり、接続情報がわかっているものとします。
> 
> 

1. 最新バージョンの SQL Server Management Studio があることを確認します。 Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。
   
   > [!IMPORTANT]
   > 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. Azure SQL Database サーバーに接続し、**[データベース]** フォルダーを右クリックして、**[データ層アプリケーションのインポート]** をクリックします
   
    ![[データ層アプリケーションのインポート] メニュー項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)
3. 新しいデータベースを Azure SQL Database に作成するには、ローカル ディスクから BACPAC ファイルをインポートするか、Azure Storage アカウントと、BACPAC ファイルのアップロード先コンテナーを選択します。
   
   ![設定のインポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)
   
   > [!IMPORTANT]
   > BACPAC を Azure Blob Storage からインポートする場合は、Standard Storage を使用します。 Premium Storage からの BACPAC のインポートはサポートされていません。
   > 
   > 
4. Azure SQL DB でのデータベースの **[新しいデータベース名]** を指定し、**[Microsoft Azure SQL Database のエディション]** (サービス階層)、**[データベースの最大サイズ]**、および **[サービスの目的]** (パフォーマンス レベル) を設定します。
   
   ![データベース設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)
5. **[次へ]**、**[完了]** の順にクリックし、Azure SQL Database サーバーの新しいデータベースに BACPAC ファイルをインポートします。
6. オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。
7. Azure ポータルを使用して、データベースとそのプロパティを表示します。

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


