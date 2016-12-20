---
title: "Azure SQL Data Warehouse の復元 (REST API) | Microsoft Docs"
description: "Azure SQL Data Warehouse を復元するための REST API タスク。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Azure SQL Data Warehouse の復元 (REST API)
> [!div class="op_single_selector"]
> * [概要][概要]
> * [ポータル][ポータル]
> * [PowerShell][PowerShell]
> * [REST ()][REST ()]
> 
> 

この記事では、REST API を使用して Azure SQL Data Warehouse を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。**  各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。  SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL server に残っていることを確認してください。 必要な DTU を計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][DTU クォータの変更の要求] に関するトピックをご覧ください。

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
データベースを復元するには:

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. [「データベースの復元要求の作成」][データベースの復元要求の作成] の操作により、復元を開始します。
3. [「データベース操作の状態」][データベース操作の状態] の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了したら、[「復旧後のデータベースの構成」][復旧後のデータベースの構成] の手順に従って、復旧されたデータベースを構成できます。
> 
> 

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
削除されたデータベースを復元するには:

1. [「削除された復元可能なデータベースの一覧表示」][削除された復元可能なデータベースの一覧表示] の操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
2. [「削除された復元可能なデータベースの取得」][削除された復元可能なデータベースの取得] の操作を使用して、復元する削除済みデータベースの詳細を取得します。
3. [「データベースの復元要求の作成」][データベースの復元要求の作成] の操作により、復元を開始します。
4. [「データベース操作の状態」][データベース操作の状態] の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、[「復旧後のデータベースの構成」][復旧後のデータベースの構成]を参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][Azure SQL Database のビジネス継続性の概要] に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: ../sql-database/sql-database-business-continuity.md
[DTU クォータの変更の要求]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[復旧後のデータベースの構成]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Azure PowerShell のインストールおよび構成方法]: ./powershell-install-configure.md
[概要]: ./sql-data-warehouse-restore-database-overview.md
[ポータル]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST ()]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[データベースの復元要求の作成]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[データベース操作の状態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[削除された復元可能なデータベースの取得]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[削除された復元可能なデータベースの一覧表示]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure App Service で Java Web アプリ]: https://portal.azure.com/
[Microsoft Web プラットフォーム インストーラー]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


