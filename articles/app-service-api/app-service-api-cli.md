<properties
	pageTitle="Azure CLI と API Apps |Microsoft Azure"
	description="Azure API Apps を使用した Mac、Linux、Windows 向け Microsoft Azure コマンドライン インターフェイスの使用方法。"
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Azure コマンド ライン インターフェイス (CLI) と API アプリ

この記事では、Mac、Linux、Windows 用 Azure コマンドライン インターフェイス (CLI) を使用して、Azure App Service で API アプリを作成、管理、削除する方法を説明します。

## 前提条件

この記事では、Azure CLI がインストールされていることと、基本的なタスクの実行方法を理解していることを前提としています。CLI の概要については、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。

> [AZURE.NOTE] [Azure サブスクリプションへの接続](../xplat-cli-connect.md)手順では、職場または学校のアカウントを使用してログインする方法と、*.publishsettings* ファイルをダウンロードする方法の 2 点を説明しています。API アプリでは、*.publishsettings* ファイルの認証方法は機能しません。これは、API アプリを操作するにはリソースの管理モード (次のセクションで説明します) を使用する必要があり、*.publishsettings* ファイルの認証方法はリソース マネージャーでは機能しないからです。

## リソース管理モードを有効にする

CLI は[サービス管理 (asm)](../virtual-machines/virtual-machines-command-line-tools.md) モードか、[リソース管理 (arm)](../xplat-cli-azure-resource-manager.md) モードで使用できます。API アプリでは、リソース管理モードを使用する必要があります。`arm` モードは既定で有効になっていないため、`config mode arm` コマンドを使用して有効にする必要があります。

	azure config mode arm

## API アプリの操作に使用できるコマンドを一覧表示する

API アプリの操作に現在利用可能なすべてのコマンドを表示するには、`apiapp` コマンドを実行します。

	azure apiapp

## サブスクリプションやリソース グループ内のすべての API アプリを一覧表示する

サブスクリプション内にあるすべての API アプリを一覧表示するには、`apiapp list` コマンドをパラメーターなしで実行します。

	azure apiapp list

この一覧には、リソース グループ、API アプリ名、パッケージ ID、各 API アプリの URL が表示されます。

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

指定したリソース グループに一覧を制限するには、グループ (`-g`) パラメーターを追加します。

	azure apiapp list -g <resource group name>

次に例を示します。

	azure apiapp list -g mygroup

一覧に API アプリのバージョンとアクセス レベル情報を追加するには、詳細 (`-d`) パラメーターを追加します。

	azure apiapp list -d

フィールドが追加された `list` 出力は次の例のようになります。

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### API アプリの詳細の一覧表示

1 つの API アプリの詳細を一覧表示するには、`apiapp show` コマンドをグループ (`-g`) パラメーターと API アプリ名 (`-n`) パラメーターと組み合わせて使用します。

	azure apiapp show -g <resource group name> -n <API app name>

次に例を示します。

	azure apiapp show -g mygroup -n SimpleDropbox

出力は次のようになります。

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## API アプリの作成

API アプリを作成する方法は、2 つあります。CLI の命令型コマンドを使用して個別に Azure リソースを作成することも、テンプレートで宣言構文を使用し、必要なリソースすべてを一緒に定義して、CLI コマンドでそのテンプレートをデプロイすることもできます。宣言型のアプローチについては、[次のステップ](#next-steps)をご覧ください。

命令型のアプローチを使用して API アプリを作成するには、次の手順を実行します。

1. 有効な場所の選択
1. 使用するリソース グループの作成または検索
2. 使用する App Service プランの作成または検索
4. API アプリの作成

### 有効な場所の選択

リソース グループを作成する際は、場所を指定する必要があります。次に API アプリで有効な場所をいくつか示します。

* 米国東部
* 米国西部
* 米国中南部
* 北ヨーロッパ
* 東アジア
* 東日本
* 西ヨーロッパ
* 東南アジア
* 西日本
* 米国中北部
* 米国中央部
* ブラジル南部
* 米国東部 2

`location list` コマンドを使用して最新の詳しい一覧を取得し、`Microsoft.AppService/apiapps` リソース プロバイダーの行をご覧ください。

	azure location list

`location list` コマンドによる出力例は次のとおりです。

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### 使用するリソース グループの作成または検索

リソース グループを作成するには、名前 (`n`) と場所 (`l`) パラメーターを指定して `group create` コマンドを使用します。

	azure group create -n <name> -l <location>

次に例を示します。

	azure group create -n "mygroup" -l "West US"

既存のリソース グループを検索するには、`group list` コマンドを使用して、API アプリの有効な場所でリソース グループを選択します。

	azure group list

### 使用する App Service プランの作成または検索

App Service プランを作成するには、`resource create` コマンドとリソースの種類パラメーター (`-r`) を使用して、作成するリソースの種類に App Service プランを指定します。

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
次に例を示します。

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

REST API での最近の変更により、`properties` (`-p`) パラメーター用に JSON 文字列が必要です。今後、`-p` パラメーターは省略可能になります。

サンプル コマンドは、この記事の執筆時点での最新 API バージョンを指定します。これ以降のバージョンがあるかどうかを確認するには、`provider show` コマンドを使用して、`resourceTypes` 配列で `sites` オブジェクトに対する `apiVersions` 配列を確認します。

	azure provider show -n Microsoft.Web --json
   
次に示すのは、コマンド出力の `sites` オブジェクトの例です。

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

既存の App Service プランを一覧表示するには、`resource list` コマンドを使用し、`-r` パラメーターのリソースの種類として App Service プランを指定します。

	azure resource list -r Microsoft.Web/serverfarms

出力は次のようになります。

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### 空の (カスタム) API アプリを作成する

空の (カスタム) API アプリを作成するには (自分でコードを記述する場合)、`apiapp create` コマンドを使用して、`Microsoft.ApiApp` Nuget パッケージ (`-u` パラメーター) を指定します。

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

次に例を示します。

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

API アプリが作成されると、出力に進捗状況がレポートされます。

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Marketplace から API アプリを作成する

Marketplace で使用可能な API アプリ パッケージの一覧を取得するには、`group template list` コマンドを使用し、カテゴリ (`-c`) パラメーターに API アプリを指定します。

	azure group template list -c apiapps

出力は次のようになります。

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Marketplace から API アプリを作成するには、`apiapap create` コマンドを使用し、作成する必要のある API アプリの名前を NuGet パッケージ (`-u`) パラメーターに指定します。

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

`-u` パラメーターに使用する値は marketplace 名の中央のセクションです。たとえば、microsoft\_com.MicrosoftSqlConnector.0.2.2 では、コマンドは次のようになります。

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
サーバー名や SQL コネクタ用接続文字列などの API アプリのテンプレート パラメーターが必要な場合、必要なデータを指定するよう求められます。パラメーター値を渡す際に、`--parameters` か `--parameters-file` を使用するオプションがあります。パラメーター ファイルの詳細については、「[新しいゲートウェイを使用する API アプリのプロビジョニング](app-service-api-arm-new-gateway-provision.md)」をご覧ください。

## 次のステップ

この記事では、個々の Azure CLI コマンドを使用してカスタムの API アプリや Marketplace テンプレートから作成する API アプリを操作する方法について説明しました。カスタム テンプレートを使用して、API アプリの作成を自動化する方法については、次のリソースをご覧ください。

* [既存のゲートウェイを使用する API アプリのプロビジョニング](app-service-api-arm-existing-gateway-provision.md)
* [新しいゲートウェイを使用する API アプリのプロビジョニング](app-service-api-arm-new-gateway-provision.md)

Azure リソース マネージャーで Azure コマンド ライン ユーティリティを使用する方法の詳細については、次のリソースをご覧ください。
 
* [Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)
* [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)
 

<!---HONumber=AcomDC_0114_2016--->