---
title: "Azure Portal: 単一の Azure SQL データベースの作成と管理 | Microsoft Docs"
description: "Azure Portal を使用して単一の Azure SQL データベースを作成および管理する方法のクイック リファレンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 5e01db2676e3515f41bf98d23595e6509c0d6805
ms.openlocfilehash: c5f50213be9cd20c82acf8dd94463e7dce0a0195
ms.lasthandoff: 02/27/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Azure Portal を使用した単一の Azure SQL データベースの作成と管理

[Azure Portal](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API、または C# を使用して、単一の Azure SQL データベースを作成および管理できます。 このトピックでは、Azure Portal を使用した場合の方法について説明します。 PowerShell を使用した場合の方法については、[Powershell を使用した単一のデータベースの作成と管理](sql-database-manage-single-databases-powershell.md)に関するページを参照してください。 Transact-SQL を使用した場合の方法については、[Transact-SQL を使用した単一のデータベースの作成と管理](sql-database-manage-single-databases-tsql.md)に関するページを参照してください。 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Azure Portal を使用した単一の Azure SQL データベースの作成

1. [Azure Portal](https://portal.azure.com/) で **[SQL データベース]** ブレードを開きます。 

    ![SQL データベース](./media/sql-database-get-started/sql-databases.png)
2. [SQL データベース] ブレードで、**[追加]** をクリックします。

    ![SQL データベースの追加](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Azure Portal を使用してデータベースを作成するチュートリアルについては、[Azure Portal を使用したデータベースの作成](sql-database-get-started.md)に関するページを参照してください。
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Azure Portal を使用して SQL データベース設定を表示および更新

1. [Azure Portal](https://portal.azure.com/) で **[SQL データベース]** ブレードを開きます。 

    ![SQL データベース](./media/sql-database-get-started/sql-databases.png)

2. 対象のデータベースをクリックし、[SQL データベース] ブレードで目的の設定をクリックします。

    ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>単一データベースのサービス レベルとパフォーマンス レベルの変更

スケールアップまたはスケールダウンするデータベースの [SQL Database] ブレードを開きます。

1. [Azure Portal](https://portal.azure.com) で **[その他のサービス]** > **[SQL Database]** の順にクリックします。
2. 変更するデータベースをクリックします。
3. **[SQL Database]** ブレードで **[価格レベル (DTU のスケール)]** をクリックします。
   
   ![[価格レベル]](./media/sql-database-manage-single-database-portal/new-tier.png)

4. 新しい階層を選択し、**[選択]**をクリックします。
   
   **[選択]** をクリックすると、価格レベルを変更するスケール要求が送信されます。 データベースのサイズに応じて、スケール操作の完了に時間がかかる場合があります ([サービス階層](sql-database-service-tiers.md)を参照してください)。
   
   > [!NOTE]
   > データベースの価格レベルを変更しても、データベースの最大サイズは変更されません。 データベースの最大サイズを変更するには、[Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) または [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) を使用します。
   >  
   
   ![価格レベルの選択](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. 右上の通知アイコン (ベル) をクリックします。
   
   ![[通知]](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. 通知のテキストをクリックすると、詳細ウィンドウが開き、要求のステータスを確認できます。

## <a name="next-steps"></a>次のステップ
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用して管理タスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、[機能](sql-database-features.md)に関するページをご覧ください。

