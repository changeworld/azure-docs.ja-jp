---
title: "TSQL で SQL Data Warehouse を作成する | Microsoft Docs"
description: "TSQL で Azure SQL Data Warehouse を作成する方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: bbfce3f3cf329792f270632e2244e33e3fafb7ef


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Transact-SQL (TSQL) を使用して SQL Data Warehouse データベースを作成する
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

この記事では、T-SQL を使用して SQL Data Warehouse を作成する方法を示します。

## <a name="prerequisites"></a>前提条件
開始するには、以下が必要です。

* **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][Azure 無料試用版]に関するページまたは [MSDN Azure クレジット][MSDN Azure クレジット]に関するページにアクセスしてください。
* **Azure SQL Server**: 詳細については、[Azure Portal での Azure SQL Database 論理サーバーの作成][Azure Portal での Azure SQL Database 論理サーバーの作成]または [PowerShell を使用したAzure SQL Database 論理サーバーの作成][PowerShell を使用したAzure SQL Database 論理サーバーの作成]に関するセクションを参照してください。
* **リソース グループ**: 使用している Azure SQL Server と同じリソース グループを使用するか、[リソース グループの作成方法][リソース グループの作成方法]に関するセクションを参照してください。
* **T-SQL の実行環境**: T-SQL の実行には、[Visual Studio][Visual Studio と SSDT のインストール]、[sqlcmd][sqlcmd]、または [SSMS][SSMS] を使用できます。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  価格の詳細については、「[SQL Data Warehouse の価格][SQL Data Warehouse の価格]」を参照してください。
>
>

## <a name="create-a-database-with-visual-studio"></a>Visual Studio でデータベースを作成する
Visual Studio に慣れていない場合は、「[Azure SQL Data Warehouse に対するクエリ (Visual Studio)][Azure SQL Data Warehouse に対するクエリ (Visual Studio)]」を参照してください。  開始するには、Visual Studio で SQL Server オブジェクト エクスプローラーを開き、SQL Data Warehouse データベースをホストするサーバーに接続します。  接続したら、 **master** データベースに対して次の SQL コマンドを実行することで、SQL Data Warehouse を作成できます。  このコマンドは、サービス目標を DW400 にしてデータベース MySqlDwDb を作成し、データベースのサイズを最大で 10 TB まで拡張できるようにします。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>sqlcmd でデータベースを作成する
コマンド プロンプトで以下のように実行することによって、sqlcmd で同じコマンドを実行することもできます。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

照合順序が指定されていない場合の既定の照合順序は COLLATE SQL_Latin1_General_CP1_CI_AS です。  `MAXSIZE` には、250 GB から 240 TB までの値を指定できます。  `SERVICE_OBJECTIVE` には、DW100 から DW2000 までの [DWU][DWU] を指定できます。  すべての有効な値の一覧については、[CREATE DATABASE][CREATE DATABASE] に関する MSDN ドキュメントを参照してください。  MAXSIZE と SERVICE_OBJECTIVE のどちらも、[ALTER DATABASE][ALTER DATABASE] T-SQL コマンドで変更できます。  データベースの作成後にその照合順序を変更することはできません。   SERVICE_OBJECTIVE を変更する際は注意が必要です。DWU を変更するとサービスが再起動され、処理中のすべてのクエリが取り消されるためです。  MAXSIZE の変更は、単なるメタデータ操作であるため、サービスが再起動されることはありません。

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][サンプル データの読み込み]、[開発][開発]、[読み込み][読み込み]、[移行][移行]の方法を確認したりできます。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Azure Portal から SQL Data Warehouse を作成する方法]: sql-data-warehouse-get-started-provision.md
[Azure SQL Data Warehouse に対するクエリ (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[読み込み]: sql-data-warehouse-overview-load.md
[サンプル データの読み込み]: sql-data-warehouse-load-sample-databases.md
[Azure Portal での Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started.md#create-logical-server-bk
[PowerShell を使用したAzure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[リソース グループの作成方法]: ../resource-group-template-deploy-portal.md#create-resource-group
[Visual Studio と SSDT のインストール]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 無料試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure クレジット]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


