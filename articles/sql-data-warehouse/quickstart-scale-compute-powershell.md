---
title: "クイックスタート: Azure SQL Data Warehouse のコンピューティングのスケールアウト - PowerShell | Microsoft Docs"
description: "Data Warehouse ユニットを調整してコンピューティング リソースをスケールアウトする PowerShell タスク。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>クイックスタート: PowerShell で Azure SQL Data Warehouse のコンピューティングをスケーリングする

PowerShell で Azure SQL Data Warehouse のコンピューティングをスケーリングします。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

このチュートリアルには、Azure PowerShell モジュール バージョン 5.1.1 以降が必要です。 現在所有しているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps.md)に関するページを参照してください。 

## <a name="before-you-begin"></a>開始する前に

このクイックスタートでは、スケーリングできる SQL データ ウェアハウスが既に用意されていることを前提とします。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成してください。 

## <a name="log-in-to-azure"></a>Azure にログインする

[Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Add-AzureRmAccount
```

使用しているサブスクリプションを確認するには、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を実行します。

```powershell
Get-AzureRmSubscription
```

既定ではない別のサブスクリプションを使用する必要がある場合は、[Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription) を実行します。

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>データ ウェアハウスの情報を調べる

一時停止して再開する予定のデータ ウェアハウスのデータベース名、サーバー名、およびリソース グループを見つけます。 

次の手順に従って、データ ウェアハウスの場所の情報を検索します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の左側のページで **[SQL データベース]** を選択します。
3. **[SQL データベース]** ページから **[mySampleDataWarehouse]** を選択します。 これにより、データ ウェアハウスが開きます。 

    ![サーバー名とリソース グループ](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. データベース名として使用されるデータ ウェアハウスの名前を書き留めます。 サーバー名とリソース グループも書き留めます。 これらは、一時停止コマンドと再開コマンドで使用します。
5. サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として最初の部分のみを使用します。 前の図では、サーバーの完全名は newserver-20171113.database.windows.net です。 PowerShell コマンドレットのサーバー名として **newserver-20171113** を使用します。

## <a name="scale-compute"></a>コンピューティングのスケーリング

SQL Data Warehouse では、Data Warehouse ユニットを調整してコンピューティング リソースを増減させることができます。 [ポータルでの作成と接続](create-data-warehouse-portal.md)では、**mySampleDataWarehouse** を作成し、それを 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには、[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell コマンドレットを使用します。 次の例では、サーバー **mynewserver-20171113** 上のリソース グループ **myResourceGroup** にホストされているデータベース **mySampleDataWarehouse** の Data Warehouse ユニットを DW300 に設定します。

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>データベース状態の確認

データ ウェアハウスの現在の状態を確認するには、[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) PowerShell コマンドレットを使用します。 これを使用すると、リソース グループ **myResourceGroup** とサーバー **mynewserver-20171113.database.windows.net** 内のデータベース **mySampleDataWarehouse** の状態が取得されます。

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

次のような結果が表示されます。

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

チェックを行ってデータベースの**状態**を確認できます。 このケースでは、データベースがオンラインであることが確認できます。  このコマンドを実行すると、状態を表す値としてオンライン、一時停止中、再開中、スケーリング、一時停止のいずれかが表示されます。

## <a name="next-steps"></a>次の手順
ここでは、データ ウェアハウスのコンピューティングをスケーリングする方法について学習しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
