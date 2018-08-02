---
title: 'クイック スタート: Azure SQL データ ウェアハウスの作成 - Azure PowerShell | Microsoft Docs'
description: Azure PowerShell を使用して、SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、データ ウェアハウスをすばやく作成します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/01/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 72ed9e921d96faea155c1da88dd32fcbd467d549
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414002"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して Azure SQL データ ウェアハウスを作成し、クエリを実行する

Azure PowerShell を使用して Azure SQL データ ウェアハウスをすばやく作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

このチュートリアルには、Azure PowerShell モジュール バージョン 5.1.1 以降が必要です。 現在所有しているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 


> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。
>
>

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


## <a name="create-variables"></a>変数の作成

このクイック スタートのスクリプトで使用する変数を定義します。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>論理サーバーの作成

[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) コマンドを使用して [Azure SQL 論理サーバー](../sql-database/sql-database-logical-servers.md)を作成します。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ログイン `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) コマンドを使用して [Azure SQL のサーバーレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL Data Warehouse サービスのファイアウォールを介して SQL データ ウェアハウスに接続できます。 次の例では、他の Azure リソースに対してのみファイアウォールを開放しています。 外部から接続できるようにするには、実際の環境に合わせて IP アドレスを変更してください。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database と SQL Data Warehouse はポート 1433 上で通信します。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL サーバーに接続することはできません。
>


## <a name="create-a-data-warehouse-with-sample-data"></a>サンプル データを使用してデータ ウェアハウスを作成する
この例では、以前に定義した変数を使用してデータ ウェアハウスを作成します。  サービス目標には DW400 を指定します、これは、データ ウェアハウス用の低コストの開始点です。 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

必要なパラメーターは以下のとおりです。

* **RequestedServiceObjectiveName**: 要求する [Data Warehouse ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)の量。 この量を増やすと、コンピューティング コストが増加します。 サポートされている値の一覧については、[メモリと同時実行の制限](memory-and-concurrency-limits.md)に関する記事をご覧ください。
* **DatabaseName**: 作成する SQL Data Warehouse の名前。
* **ServerName**: 作成の際に使用するサーバーの名前。
* **ResourceGroupName**: 使用するリソース グループ。 サブスクリプションで使用可能なリソース グループを調べるには Get-AzureResource を使用します。
* **Edition**: SQL Data Warehouse を作成するには、"DataWarehouse" に設定する必要があります。

省略可能なパラメーターは次のとおりです。

- **CollationName**: 照合順序が指定されていない場合の既定の照合順序は SQL_Latin1_General_CP1_CI_AS です。 データベースの照合順序は変更できません。
- **MaxSizeBytes**: データベースの既定の最大サイズは 10 GB です。

パラメーター オプションについて詳しくは、「[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)」をご覧ください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイック スタート チュートリアルは、このクイック スタートに基づいています。 

> [!TIP]
> 引き続きクイック スタート チュートリアルの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>次の手順

ここではデータ ウェアハウスを作成し、ファイアウォール規則を作成し、データ ウェアハウスに接続していくつかのクエリを実行しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。
> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
