<properties 
   pageTitle="Azure SDK for Node.js を使用して Azure Data Lake Store を管理する | Azure" 
   description="Data Lake Store アカウントとファイル システムを管理する方法について説明します。" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Azure SDK for Node.js を使用して Azure Data Lake Store を管理する

> [AZURE.SELECTOR]
- [ポータル](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Azure ADK for Node.js を使用して、Azure Data Lake Store アカウントとファイル システムを管理できます。

- アカウント管理: 作成、取得、一覧表示、更新、および削除。
- ファイル システムの管理: 作成、取得、アップロード、追加、ダウンロード、読み取り、削除、一覧表示。

**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- **Azure Data Lake Store アカウント**。「[Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」を参照してアカウントを作成します。
- **Data Lake Analytics アカウントへのアクセス許可を持つサービス プリンシパル**。「[Azure リソース マネージャーでのサービス プリンシパルの認証](../resource-group-authenticate-service-principal.md)」を参照してください。

## SDK のインストール

次のコマンドを使用して SDK をインストールします。

1. [Node.js](https://nodejs.org/) をインストールします。
2. コマンド プロンプト、terminal または bash ウィンドウで次のコマンドを実行します。

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Node.js のサンプル

次の例は、Data Lake Store アカウントにファイルを作成してデータを追加します。

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##関連項目 

- [Azure SDK for Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Node.js を使用して Azure Data Lake Analytics を管理する](../data-lake-analytics/data-lake-analytics-manage-use-nodejs.md)

<!---HONumber=AcomDC_0413_2016-->