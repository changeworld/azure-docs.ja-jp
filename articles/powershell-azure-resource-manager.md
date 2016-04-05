<properties 
	pageTitle="リソース マネージャーでの Azure PowerShell | Microsoft Azure" 
	description="Azure PowerShell を使用して複数のリソースをリソース グループとして Azure にデプロイする方法について、概要を示します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーでの Azure PowerShell の使用

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure リソース マネージャーでは、Azure リソースに関するまったく新しい考え方が導入されています。個々のリソースを作成して管理するのではなく、まず、ブログ、フォト ギャラリー、SharePoint ポータル、Wiki など、ソリューション全体の構想を練ります。ソリューションの宣言型表現であるテンプレートを使用して、ソリューションをサポートするために必要なすべてのリソースが含まれているリソース グループを作成します。その後、そのリソース グループを論理ユニットとして管理してデプロイします。

このチュートリアルでは、Azure リソース マネージャーで Azure PowerShell を使用する方法について説明します。ここでは、サポートするために必要なすべてのリソースを含む、SQL データベースを備えた Azure でホストされる Web アプリケーションのリソース グループを作成し、デプロイするプロセスを説明します。

## 前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure アカウント
  + [無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。明示的に設定を変更して課金を求めない限り、クレジット カードに課金されることはありません。
  
  + [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。MSDN サブスクリプションにより、有料の Azure サービスを使用できるクレジットが毎月提供されます。
- Azure PowerShell 1.0このリリースとそのインストール方法については、「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」を参照してください。

このチュートリアルは、PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。

## デプロイ対象

このチュートリアルでは、Azure PowerShell を使用して Web アプリと SQL Database をデプロイします。ただし、この Web アプリと SQL Database ソリューションは、連携して動作する数種類のリソースで構成されています。実際にデプロイするリソースは次のとおりです。

- データベースをホストする SQL Server
- データを格納する SQL Database
- Web アプリにデータベースへの接続を許可するファイアウォール ルール
- Web アプリの機能とコストを定義するための App Service プラン
- Web アプリを実行するための Web サイト
- データベースへの接続文字列を格納するための Web 構成 

## コマンドレットのヘルプの取得

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

	Get-Help <cmdlet-name> -Detailed

たとえば、Get-AzureRmResource コマンドレットのヘルプを取得するには、次のように入力します。

	Get-Help Get-AzureRmResource -Detailed

ヘルプの概要と Resources モジュールのコマンドレットの一覧を取得するには、次のように入力します。

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

次のような出力が表示されます。

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

コマンドレットの完全なヘルプを取得するには、次の形式でコマンドを入力します。

	Get-Help <cmdlet-name> -Full
  
## Azure アカウントへのログイン

ソリューションを操作する前に、ご使用のアカウントにログインする必要があります。

Azure アカウントにログインするには、**Login-AzureRmAccount** コマンドレットを使用します。

    PS C:\> Login-AzureRmAccount

このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

アカウント設定の有効期限が切れるため、ときどき更新する必要があります。アカウント設定を更新するには、**Login-AzureRmAccount** をもう一度実行します。

>[AZURE.NOTE] リソース マネージャー モジュールでは Login-AzureRmAccount が必要になります。発行設定ファイルでは不十分です。

## リソースの種類の場所を取得する

リソースをデプロイするときに、リソースをホストする場所を指定する必要があります。すべてのリージョンですべてのリソースの種類がサポートされているわけではありません。Web アプリと SQL Database をデプロイする前に、どのリージョンでこれらのリソースの種類がサポートされているかを確認する必要があります。リソース グループには、異なるリージョンに存在するリソースを含めることができます。ただし、可能であれば、パフォーマンスを最適化するために同じ場所にリソースを作成することをお勧めします。特に、アプリケーションがデータベースにアクセスする場所とデータベースが同じ場所にあるかどうかを確認することをお勧めします。

各リソースの種類をサポートする場所を取得するには、**Get-AzureRmResourceProvider** コマンドレットを使用する必要があります。まず、このコマンドによって表示される情報を見てみましょう。

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace は、関連するリソースの種類のコレクションを表します。通常、これらの名前空間は Azure で作成するサービスに一致します。**Unregistered** として示されたリソース プロバイダーを使用する場合は、**Register-AzureRmResourceProvider** コマンドレットを実行して登録するプロバイダーの名前空間を指定することで、そのリソース プロバイダーを登録できます。ほとんどの場合、このチュートリアルで使用するリソース プロバイダーは、サブスクリプションで既に登録されています。

その名前空間を指定すると、プロバイダーについての詳細が表示されます。

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Web サイトなどの特定の種類のリソースについてサポートされている場所に出力を制限するには、次のコマンドを使用します。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
次のように出力されます。

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

表示される場所は、以前の結果と多少異なる場合があります。結果が異なる場合があるのは、組織の管理者がお使いのサブスクリプションで使用できるリージョンを制限するポリシーを作成したか、お住まいの国の租税政策に関する制限があるためです。

データベースに対して同じコマンドを実行します。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

これらのリソースは多くのリージョンで利用可能であることがわかります。このトピックでは、**West US** を使用しますが、サポートされているいずれかのリージョンを指定できます。

## リソース グループの作成

チュートリアルのこのセクションでは、リソース グループを作成する手順について説明します。リソース グループは、ライフサイクルが同じである、ソリューション内のすべてのリソースのコンテナーとして機能します。チュートリアルの後半で、このリソース グループに Web アプリと SQL Database をデプロイします。

リソース グループを作成するには、**New-AzureRmResourceGroup** コマンドレットを使用します。

コマンドは **Name** パラメーターを使用してリソース グループの名前を指定し、**Location** パラメーターを使用して場所を指定します。前のセクションで検出した内容に基づいて、"West US" を場所に指定します。

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

リソース グループが正常に作成されました。


## リソースで使用可能な API バージョンの取得

テンプレートをデプロイするとき、リソースの作成に使用する API バージョンを指定する必要があります。利用可能な API バージョンは、リソース プロバイダーがリリースする REST API のバージョンに一致します。リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。そのため、テンプレートで指定した API のバージョンにより、テンプレートを作成する時に利用可能なプロパティが変わります。一般的に、新しいテンプレートを作成するときは、最新の API バージョンを選択します。既存のテンプレートがある場合は、デプロイを変更せずに済む API バージョンの使用を続けるか、テンプレートを最新版に更新して新しい機能を最大限に活用するかを決定できます。

この手順は一見複雑に見えますが、お使いのリソースで利用可能な API バージョンを検出するのは難しくありません。もう一度 **Get-AzureRmResourceProvider** コマンドを使用します。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

ご覧のように、この API は頻繁に更新されています。通常、同じバージョン番号の API はリソース プロバイダー内のすべてのリソースで利用可能です。唯一の例外は、ある時点でリソースが追加または削除された場合です。サーバー ファームのリソースには同じ API バージョンを利用できると想定しているものの、利用可能な API バージョンが異なる可能性がある場合は、該当するリソースを再確認してください。

データベースについては、次のように表示されます。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## テンプレートの作成

このトピックでは、テンプレートの作成方法やテンプレートの構造については説明しません。詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」をご覧ください。デプロイするテンプレートを以下に示します。このテンプレートでは前のセクションで取得した API バージョンが使用されていることに注意してください。すべてのリソースを同じリージョンに配置するために、テンプレート式 **resourceGroup().location** を使ってリソース グループの場所を使用します。

パラメーターのセクションにも注意してください。このセクションでは、リソースをデプロイするときに指定できる値を定義します。これらの値は、このチュートリアルの後半で使用します。

テンプレートをコピーして .json ファイルとしてローカルで保存できます。このチュートリアルでは、このテンプレートの保存場所は c:\\Azure\\Templates\\azuredeploy.json と想定していますが、アクセスしやすい任意の場所に、要件に適った名前で保存できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## テンプレートのデプロイ

リソース グループとテンプレートの作成が終わり、これでテンプレート内で定義されたインフラストラクチャをリソース グループにデプロイする準備が整いました。**New-AzureRmResourceGroupDeployment** コマンドレットを使用してリソースをデプロイします。基本的な構文は次のようになります。

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

リソース グループとテンプレートの場所を指定します。テンプレートがローカルでない場合は、**-TemplateUri** パラメーターを使用してテンプレートの URI を指定できます。**-Mode** パラメーターを **Incremental** または **Complete** のいずれかに設定できます。既定では、リソース マネージャーはデプロイ中に増分更新を実行するため、**増分**が必要な場合に **-Mode** を設定する必要はありません。これらのデプロイ モード間の相違については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

###動的なテンプレート パラメーター

PowerShell に慣れている場合は、マイナス記号 (-) を入力して Tab キーを押すことで、コマンドレットで利用可能なパラメーターを順番に表示できることをご存じのことと思われます。この機能は、テンプレートで定義するパラメーターでも同様に使用できます。テンプレート名を入力すると、コマンドレットがすぐにテンプレートをフェッチし、解析して、テンプレート パラメーターをコマンドに動的に追加します。これにより、テンプレート パラメーターの値の指定が非常に簡単になります。また、必須のパラメーター値を忘れた場合は、PowerShell から値を求められます。

パラメーターが含まれている完全なコマンドを以下に示します。リソースの名前には独自の値を指定できます。

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

コマンドを入力すると、不足している必須パラメーター **administratorLoginPassword** を入力するように求められます。パスワードを入力すると、セキュリティ保護された文字列値が隠されます。この対策によって、プレーンテキストでパスワードを提供することの危険性が解消されます。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

テンプレートに、テンプレートをデプロイするコマンドに、パラメーターのいずれかと一致する名前のパラメーターが含まれている場合 (たとえば、[New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) コマンドレットの **ResourceGroupName** パラメーターと同じ名前のパラメーターである **ResourceGroupName** がテンプレートに含まれている場合など)、接尾辞に **FromTemplate** があるパラメーター (**ResourceGroupNameFromTemplate** など) に値を指定するように求められます。一般的に、このような混乱を防ぐために、デプロイメント処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

コマンドを実行してリソースが作成されると、メッセージが返されます。最終的に、デプロイの結果が表示されます。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

わずかな手順で、複雑な Web サイトに必要なリソースを作成してデプロイしました。

## リソース グループに関する情報を取得します。

リソース グループを作成したら、リソース マネージャー モジュールでコマンドレットを使用してリソース グループを管理できます。

- サブスクリプションのすべてのリソース グループを取得するには、**Get-AzureRmResourceGroup** コマンドレットを使用します。

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      特定のリソース グループだけを取得する場合は、**Name** パラメーターを指定します。
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- リソース グループのリソースを取得するには、**Find-AzureRmResource** コマンドレットとその **ResourceGroupNameContains** パラメーターを使用します。パラメーターがない場合、Find-AzureRmResource は Azure サブスクリプション内のすべてのリソースを取得します。

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- 上記のテンプレートには、1 つのリソースにタグが含まれています。タグを使用して、サブスクリプションのリソースを論理的に整理できます。**Find-AzureRmResource** コマンドと **Find-AzureRmResourceGroup** コマンドを使用して、リソースをタグで照会します。

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      タグを使用して実行できることはほかにも多数あります。詳細については、[タグを使用した Azure リソースの整理](resource-group-using-tags.md)に関するページを参照してください。

## リソース グループへの追加

リソース グループにリソースを追加するには、**New-AzureRmResource** コマンドレットを使用できます。ただし、この方法でリソースを追加した場合は、その新しいリソースがテンプレート内には存在しないため、将来混乱が生じる可能性があります。元のテンプレートを再度デプロイすると、不完全なソリューションがデプロイされることになります。頻繁にデプロイしていれば、新しいリソースをテンプレートに追加して再度デプロイする方が簡単で確実であることがわかります。

## リソースの移動

新しいリソース グループに、既存のリソースを移動できます。例については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

## リソース グループの削除

- リソース グループからリソースを削除するには、**Remove-AzureRmResource** コマンドレットを使用します。このコマンドレットはリソースを削除しますが、リソース グループは削除しません。

	このコマンドは、TestRG1 リソース グループから TestSite Web サイトを削除します。

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- リソース グループを削除するには、**Remove-AzureRmResourceGroup** コマンドレットを使用します。このコマンドレットは、リソース グループとそのリソースを削除します。

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## 次のステップ

- リソース マネージャーのテンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートをデプロイする方法の詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)」を参照してください。
- プロジェクトのデプロイの詳細な例については、[Azure でマイクロサービスを予測どおりにデプロイする](app-service-web/app-service-deploy-complex-application-predictably.md)方法に関するページを参照してください。
- 失敗したデプロイのトラブルシューティングについては、「[Azure でのリソース グループのデプロイのトラブルシューティング](./resource-manager-troubleshoot-deployments-powershell.md)」を参照してください。

<!---HONumber=AcomDC_0330_2016-->