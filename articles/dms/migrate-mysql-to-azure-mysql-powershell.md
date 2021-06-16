---
title: 'PowerShell: DMS を使用して MySQL データベースから Azure Database for MySQL へのオフライン移行を実行する'
titleSuffix: Azure Database Migration Service
description: PowerShell スクリプトから Azure Database Migration Service を使用して、オンプレミスの MySQL データベースを Azure Database for MySQL に移行する方法について説明します。
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: eea4a539c8a2b17a9a6280a8f847e68542d1a8d5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950652"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>PowerShell と Azure Database Migration Service を使用して MySQL を Azure Database for MySQL にオフラインで移行する

この記事では、Microsoft Azure PowerShell から Azure Database Migration Service のオフライン移行機能を使用して、オンプレミス インスタンスに復元された MySQL データベースを Azure Database for MySQL に移行します。 この記事に記載されている一連の PowerShell スクリプトを順に実行することで、MySQL データベースを Azure にオフライン移行することができます。 このチュートリアルで取り上げる PowerShell スクリプトの完成版は、[GitHub リポジトリ](https://github.com/Azure/azure-mysql/tree/master/Azure%20DMS%20-%20MySQL%20Offline%20Migration%20Script)からダウンロードできます。


> [!NOTE]
> 現在、Az.DataMigration モジュールを使用してデータベースの移行を完遂することはできません。 その間は、[DMS Rest API](/rest/api/datamigration/tasks/get) を使用し、移行の自動化を可能にするサンプル PowerShell スクリプトが "現状有姿で" 提供されます。 Az.DataMigration モジュールと Azure CLI の公式サポートが追加された時点で、このスクリプトは変更されるか、非推奨となる予定です。 

> [!NOTE]
> Amazon Relational Database Service (RDS) for MySQL および Amazon Relational Database Service (RDS) for Amazon Aurora (MySQL ベース) も移行元としてサポートされます。

> [!IMPORTANT]
> オンライン移行では、[データイン レプリケーション](../mysql/concepts-data-in-replication.md)と共に、[MyDumper/MyLoader](https://centminmod.com/mydumper.html) などのオープンソース ツールを使用できます。


この記事を参考に自動化するシナリオでは、ソース データベースとターゲット データベースの名前は同じであっても、異なっていてもかまいません。また、移行の一環として、ターゲット データベース内のテーブルのうち、同じ名前とテーブル構造を共有する一部または全部のテーブルを移行しなければなりません。 この記事は、ソースとして MySQL データベース インスタンスを、ターゲットとして Azure Database for MySQL を想定していますが、ソース サーバーの名前と資格情報さえ変更すれば Azure Database for MySQL 間の移行にも使用できます。 また、MySQL サーバーの下位バージョン (v5.6 以上) から上位バージョンへの移行にも対応します。

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

この記事では、次のことについて説明します。
> [!div class="checklist"]
>
> * データベース スキーマを移行する。
> * リソース グループを作成します。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service インスタンスで移行プロジェクトを作成します。
> * MySQL のオフライン移行機能を使用するように移行プロジェクトを構成する。
> * 移行を実行する。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

* アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
* バージョン 5.6 以上の MySQL データベースをオンプレミスに用意します。 ない場合は、[MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 以上をダウンロードしてインストールしてください。
* [Azure Database for MySQL でインスタンスを作成します](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)。 Workbench アプリケーションを使用してデータベースを接続、作成する方法の詳細については、[MySQL Workbench を使用した接続とデータの照会](../mysql/connect-workbench.md)に関するページを参照してください。 Azure Database for MySQL のバージョンは、オンプレミスの MySQL のバージョンと同じかそれ以上である必要があります。 たとえば、MySQL 5.7 は Azure Database for MySQL 5.7 に移行したり、8 にアップグレードしたりすることができます。  
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続が確立されます。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、*Microsoft.Sql* サービスの [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。 Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループの規則によって、Storage および AzureMonitor の ServiceTag のアウトバウンド ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* Azure Database Migration Service がソース MySQL Server (既定では TCP ポート 3306) にアクセスできるよう、Windows ファイアウォールを開放して仮想ネットワークからの接続を許可します。
* ソース データベースの手前でファイアウォール アプライアンスを使用する場合は、移行対象のソース データベースに Azure Database Migration Service がアクセスできるよう、その仮想ネットワークからの接続を許可するファイアウォール規則を追加することが必要となる場合があります。
* Azure Database Migration Service の仮想ネットワークからターゲット データベースにアクセスできるよう、サーバーレベルの[ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成するか、ターゲット Azure Database for MySQL の [VNET サービス エンドポイントを構成](../mysql/howto-manage-vnet-using-portal.md)します。
* ソース MySQL は、サポートされている MySQL Community Edition で実行されている必要があります。 MySQL インスタンスのバージョンを確認するには、MySQL ユーティリティまたは MySQL Workbench で、次のコマンドを実行します。

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL は、InnoDB テーブルのみをサポートします。 MyISAM テーブルを InnoDB に変換するには、[MyISAM から InnoDB へのテーブルの変換](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)に関する記事を参照してください。
* ユーザーには、ソース データベースにあるデータの読み取り権限が必要です。
* このガイドでは、[インストール ガイド](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)に従ってインストールできる、PSEdition Core の PowerShell v7.1 を使用します。
* [Install-Module PowerShell コマンドレット](/powershell/module/powershellget/Install-Module)を使用して、PowerShell ギャラリーから次のモジュールをダウンロードしてインストールします。必ず [管理者として実行] を使用して PowerShell コマンド ウィンドウを開いてください。
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>データベース スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを転送するには、ソース データベースからスキーマを抽出し、ターゲット データベースに適用する必要があります。 スキーマを抽出するには、mysqldump と `--no-data` パラメーターを使用できます。 そのためには、ソースの MySQL データベースとターゲットの Azure Database for MySQL の両方に接続できるマシンが必要です。

mysqldump を使用してスキーマをエクスポートするには、次のコマンドを実行します。

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

次に例を示します。

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

ターゲットの Azure Database for MySQL にスキーマをインポートするには、次のコマンドを実行します。

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

次に例を示します。

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

スキーマに外部キーが含まれている場合、移行中の並列データ読み込みが移行タスクによって処理されます。 スキーマの移行中に外部キーを削除する必要はありません。

データベースにトリガーが含まれている場合、ソースからのフル データ移行の前に、ターゲットにデータ整合性が適用されます。 移行時はターゲットのすべてのテーブルのトリガーを無効にし、移行の完了後にそれらのトリガーを有効にすることをお勧めします。

MySQL Workbench から、ターゲット データベースに対して次のスクリプトを実行し、drop trigger スクリプトと add trigger スクリプトを抽出します。

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

その結果に含まれる生成された drop trigger クエリ (DropQuery 列) を実行して、ターゲット データベースからトリガーを削除します。 add trigger クエリは保存できます。データ移行の完了後に使用します。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにログインする

[Azure PowerShell でのログイン](/powershell/azure/authenticate-azureps)に関する記事にある手順に従って、PowerShell から [Connect-AzAccount PowerShell コマンド](/powershell/module/az.accounts/connect-azaccount)を使用して Azure サブスクリプションにサインインします。

次のスクリプトは、ログイン後に PowerShell セッションの既定のサブスクリプションを作成すると共に、コンソール ログの書式設定のためのヘルパー ログ関数を作成するものです。

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

リソース プロバイダーの登録は、Azure サブスクリプションごとに 1 回だけ行う必要があります。 この登録なしに、**Azure Database Migration Service** のインスタンスを作成することはできません。

リソース プロバイダーの登録は、[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) コマンドを使用して行います。 **Azure Database Migration Service** に必要なリソース プロバイダーを登録するスクリプトを次に示します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 リソース グループは、DMS のリソースを作成する前に作成します。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用してリソース グループを作成します。

次の例では、"*米国西部 2*" リージョンの既定のサブスクリプション *mySubscription* に *myResourceGroup* という名前のリソース グループを作成します。

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを作成する

新しい Azure Database Migration Service インスタンスを作成するには、[New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice) コマンドを使用します。 このコマンドでは、次のパラメーターが必要です。
* *Azure リソース グループ名*。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して前述の Azure リソース グループを作成し、パラメーターとしてその名前を指定できます。
* *サービス名*。 Azure Database Migration Service に使用する一意のサービス名に対応する文字列。 
* *場所*。 サービスの場所を指定します。 米国西部や東南アジアなど、Azure データ センターの場所を指定してください。
* *SKU*。 このパラメーターは、DMS SKU 名に対応します。 現在サポートされている SKU 名は、*Standard_1vCore*、*Standard_2vCores*、*Standard_4vCores*、*Premium_4vCores* です。
* *仮想サブネット識別子*。 サブネットの情報は、[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) コマンドを使用して取得できます。 

次のスクリプトでは、*myVirtualNetwork* 仮想ネットワークと *default* という名前のサブネットが存在するという前提で、**手順 3.** で作成した同じリージョン内のリソース グループに、*myDmService* という名前の Database Migration Service を作成します。

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

Azure Database Migration Service インスタンスを作成した後、移行プロジェクトを作成します。 移行プロジェクトには、実行する必要がある移行の種類が指定されます。

次のスクリプトでは、MySQL から Azure Database for MySQL へのオフライン移行を実行するための *myfirstmysqlofflineproject* という名前の移行プロジェクトを、**手順 4.** で作成した同じリージョン内の Database Migration Service インスタンスに作成します。

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>ソースとターゲットの接続用のデータベース接続情報オブジェクトを作成する

移行プロジェクトの作成後、データベース接続情報を作成します。 移行プロセス中、ソース サーバーおよびターゲット サーバーへの接続には、この接続情報が使用されます。

次のスクリプトは、ソースとターゲットの MySQL インスタンスについて、それぞれのサーバー名、ユーザー名、パスワードを受け取って、接続情報オブジェクトを作成するものです。 ユーザーは、ソースとターゲットの MySQL インスタンスのパスワードを入力するよう求められます。 スクリプトをサイレントに実行するのであれば、Azure Key Vault から資格情報をフェッチすることができます。 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>ターゲット データベースからテーブル名のリストを抽出する

データベース テーブルのリストは、移行タスクと接続情報を使用して抽出できます。 マッピングと検証を適切に行えるよう、テーブル リストは、ソース データベースとターゲット データベースの両方から抽出されます。 

次のスクリプトは、ソース データベースとターゲット データベースの名前を受け取り、*GetUserTablesMySql* 移行タスクを使用して、それらのデータベースからテーブル リストを抽出します。 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>ユーザーの構成に基づいてテーブルのマッピングを作成する

移行タスクを構成する過程で、ソース テーブルとターゲット テーブルとの間のマッピングを作成することになります。 マッピングはテーブル名レベルですが、マッピング対象となるテーブルの構造 (列数、列名、データ型など) はまったく同じであることが前提です。

次のスクリプトは、**手順 7.** で抽出したターゲットとソースのテーブル リストに基づいてマッピングを作成します。 データの一部を読み込む場合、ユーザーはテーブルのリストを指定することで、不要なテーブルを除外することができます。 ユーザーが何も指定しなければ、すべてのターゲット テーブルがマッピングされます。 このスクリプトでは、ソースに同じ名前のテーブルが存在するかどうかもチェックされます。 そのテーブル名がソースに存在しない場合、ターゲット テーブルは移行から除外されます。 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>移行タスクの入力を作成して構成する

テーブルのマッピングを作成したら、移行タスクに対する *Migrate.MySql.AzureDbForMySql* タイプの入力を作成し、プロパティを構成します。

次のスクリプトでは、移行タスクを作成して、接続、データベース名、テーブル マッピングを設定しています。

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>パフォーマンス チューニングのパラメーターを構成する

PowerShell モジュールには、オプション パラメーターがいくつかあり、それらを環境に応じてチューニングすることができます。 これらのパラメーターを使用して、移行タスクのパフォーマンスを高めることができます。 これらのパラメーターはいずれも省略可能であり、既定値は NULL です。

> [!NOTE]
> Premium SKU では、次のパフォーマンス構成により、移行中のスループットが向上することが判明しています。
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 秒
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

次のスクリプトは、ユーザーのパラメーター値を受け取って、移行タスクのプロパティにそれらのパラメーターを設定します。

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>移行タスクを作成して実行する

タスクの入力を構成したら、そのタスクを作成してエージェントで実行することになります。 このスクリプトは、タスクの実行をトリガーし、移行の完了を待ちます。

次のスクリプトは、構成済みの移行タスクを呼び出し、その完了を待つものです。

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Database Migration Service を削除する

一度作成したインスタンスを再利用できるよう、同じ Database Migration Service は複数の移行に使用することができます。 今後 Database Migration Service を使用しない場合は、[Remove-AzDataMigrationService](/powershell/module/az.datamigration/remove-azdatamigrationservice?) コマンドを使用してサービスを削除してください。

次のスクリプトは、Azure Database Migration Service インスタンスとそれに関連付けられているプロジェクトを削除します。

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>次の手順

* DMS を使用した移行の実行時の既知の問題と制限事項については、[Azure Database Migration Service の一般的な問題](./known-issues-troubleshooting-dms.md)に関する記事を参照してください。
* DMS の使用中に発生するソース データベースの接続の問題のトラブルシューティングについては、[ソース データベースへの接続時の問題](./known-issues-troubleshooting-dms-source-connectivity.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for MySQL の詳細については、「[Azure Database for MySQL とは](../mysql/overview.md)」の記事を参照してください。
* ポータルから DMS を使用する方法のチュートリアルについては、「[チュートリアル: DMS を使用して MySQL を Azure Database for MySQL にオフラインで移行する](./tutorial-mysql-azure-mysql-offline-portal.md)」の記事を参照してください。