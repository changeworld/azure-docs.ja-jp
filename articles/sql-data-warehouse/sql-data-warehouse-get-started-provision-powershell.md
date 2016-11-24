---
title: "PowerShell を使用して SQL Data Warehouse を作成する | Microsoft Docs"
description: "PowerShell を使用して SQL Data Warehouse を作成します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 7107c64a25b3f60d7789cec57181eec9ce844f6b


---
# <a name="create-sql-data-warehouse-using-powershell"></a>PowerShell を使用して SQL Data Warehouse を作成する
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

この記事では、PowerShell を使用して SQL Data Warehouse を作成する方法を示します。

## <a name="prerequisites"></a>前提条件
開始するには、以下が必要です。

* **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][Azure 無料試用版]に関するページまたは [MSDN Azure クレジット][MSDN Azure クレジット]に関するページにアクセスしてください。
* **Azure SQL Server**: 詳細については、[Azure Portal での Azure SQL Database 論理サーバーの作成][Azure Portal での Azure SQL Database 論理サーバーの作成]または [PowerShell を使用したAzure SQL Database 論理サーバーの作成][PowerShell を使用したAzure SQL Database 論理サーバーの作成]に関するセクションを参照してください。
* **リソース グループ**: 使用している Azure SQL Server と同じリソース グループを使用するか、[リソース グループの作成方法][リソース グループの作成方法]に関するセクションを参照してください。
* **PowerShell バージョン 1.0.3 以降**: **Get-Module -ListAvailable -Name Azure** を実行することで、バージョンを確認できます。  最新バージョンは、[Microsoft Web プラットフォーム インストーラー][Microsoft Web プラットフォーム インストーラー]からインストールできます。  最新バージョンのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  価格の詳細については、「[SQL Data Warehouse の価格][SQL Data Warehouse の価格]」を参照してください。
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse の作成
1. Windows PowerShell を開きます。
2. このコマンドレットを実行して、Azure リソース マネージャーにログインします。

    ```Powershell
    Login-AzureRmAccount
    ```
3. 現在のセッションに使用するサブスクリプションを選択します。

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. データベースを作成します。 この例では、サービス目標レベルが "DW400" で名前が "mynewsqldw" のデータベースを、"mywesteuroperesgp1" という名前のリソース グループ内の "sqldwserver1" という名前のサーバーに作成します。

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

必要なパラメーターは以下のとおりです。

* **RequestedServiceObjectiveName**: 要求する [DWU][DWU] の量。  サポートされている値は、DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500、DW2000、DW3000、および DW6000 です。
* **DatabaseName**: 作成する SQL Data Warehouse の名前。
* **ServerName**: 作成の際に使用するサーバーの名前 (V12 にする必要があります)。
* **ResourceGroupName**: 使用するリソース グループ。  サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
* **Edition**: SQL Data Warehouse を作成するには、"DataWarehouse" に設定する必要があります。

省略可能なパラメーターは次のとおりです。

* **CollationName**: 照合順序が指定されていない場合の既定の照合順序は SQL_Latin1_General_CP1_CI_AS です。  データベースの照合順序は変更できません。
* **MaxSizeBytes**: データベースの既定の最大サイズは 10 GB です。

パラメーターのオプションの詳細については、「[New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]」と「[CREATE DATABASE (Azure SQL Data Warehouse)][CREATE DATABASE (Azure SQL Data Warehouse)]」を参照してください。

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][サンプル データの読み込み]、[開発][開発]、[読み込み][読み込み]、[移行][移行]の方法を確認したりできます。

プログラムで SQL Data Warehouse を管理する方法の詳細については、[PowerShell コマンドレットと REST API][PowerShell コマンドレットと REST API]の使用方法に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[移行]: ./sql-data-warehouse-overview-migrate.md
[開発]: ./sql-data-warehouse-overview-develop.md
[読み込み]: ./sql-data-warehouse-load-with-bcp.md
[サンプル データの読み込み]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell コマンドレットと REST API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[ファイアウォール規則]: ../sql-database-configure-firewall-settings.md

[Azure PowerShell のインストールおよび構成方法]: ../powershell-install-configure.md
[Azure Portal から SQL Data Warehouse を作成する方法]: ./sql-data-warehouse-get-started-provision.md
[Azure Portal での Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started.md#create-logical-server-bk
[PowerShell を使用したAzure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[リソース グループの作成方法]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[CREATE DATABASE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 無料試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure クレジット]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


