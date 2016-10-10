<properties
   pageTitle="Azure SQL Data Warehouse の復元 (REST API) | Microsoft Azure"
   description="Azure SQL Data Warehouse を復元するための REST API タスク。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# Azure SQL Data Warehouse の復元 (REST API)

> [AZURE.SELECTOR]
- [概要][]
- [ポータル][]
- [PowerShell][]
- [REST ()][]

この記事では、REST API を使用して Azure SQL Data Warehouse を復元する方法について説明します。

## 開始する前に

**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL server に残っていることを確認してください。必要な DTU を計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][]に関するトピックをご覧ください。

## アクティブまたは一時停止中のデータベースを復元する

データベースを復元するには:

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. 「[データベースの復元要求の作成][]」の操作を使用して、復元を開始します。
3. 「[データベース操作の状態][]」の操作を使用して、復元の状態を追跡します。

>[AZURE.NOTE] 復元が完了したら、「[復旧後のデータベースの構成][]」の手順に従って、復旧されたデータベースを構成できます。

## 削除されたデータベースの復元

削除されたデータベースを復元するには:

1.	「[削除された復元可能なデータベースの一覧表示][]」の操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
2.	「[削除された復元可能なデータベースの取得][]」の操作を使用して、復元する削除済みデータベースの詳細を取得します。
3.	「[データベースの復元要求の作成][]」の操作を使用して、復元を開始します。
4.	「[データベース操作の状態][]」の操作を使用して、復元の状態を追跡します。

>[AZURE.NOTE] 復元が完了した後にデータベースを構成する方法については、「[復旧後のデータベースの構成][]」を参照してください。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: ./sql-database-business-continuity.md
[DTU クォータの変更の要求]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[復旧後のデータベースの構成]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
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
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0928_2016-->