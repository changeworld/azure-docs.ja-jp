<properties 
	pageTitle="GitHub リポジトリにリンクされる Web アプリのデプロイ" 
	description="GitHub リポジトリのプロジェクトを含む Web アプリをデプロイするには、Azure Resource Manager テンプレートを使用します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="tomfitz"/>

# GitHub リポジトリにリンクされる Web アプリのデプロイ

このトピックでは、GitHub リポジトリ内のプロジェクトにリンクされる Web アプリをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、「[Web App Linked to GitHub template (GitHub にリンクされる Web アプリ用のテンプレート)](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)」を参照してください。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## デプロイ対象

このテンプレートを使用して、GitHub 内のプロジェクトのコードを含む Web アプリケーションをデプロイします。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## パラメーター

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL

デプロイするプロジェクトを含む GitHub リポジトリの URL。このパラメーターは既定値を含みますが、この値は、リポジトリに URL を指定する方法を示すことのみを目的としています。この値はテンプレートのテスト時に使用できますが、テンプレートを使用する場合には、独自のリポジトリに URL を指定します。

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### 分岐

アプリケーションのデプロイ時に使用するリポジトリの分岐。既定値は master ですが、デプロイするリポジトリ内のいずれかの分岐の名前を指定することもできます。

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## デプロイ対象のリソース

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Web アプリ

GitHub 内のプロジェクトにリンクされる Web アプリを作成します。

**siteName** パラメーターに Web アプリの名前を、**siteLocation** パラメーターに Web アプリの場所を指定します。テンプレートの **DependsOn** 要素に、Web アプリをサービス ホスティング プランに依存するものとして定義します。ホスティング プランに依存するため、ホスティング プランの作成が完了するまで、Web アプリは作成されません。**DependsOn** 要素は、デプロイの順序を指定するためにのみ使用されます。Web アプリがホスティング プランに依存するとマークしない場合、Azure Resource Mananger は両方のリソースを同時に作成しようとします。このとき、ホスティング プランの前に Web アプリが作成された場合はエラーが発生することがあります。

また、Web アプリには **resources** セクションに定義される子リソースがあります。この子リソースは、Web アプリと共にデプロイされるプロジェクトのソース管理を定義します。このテンプレートでは、ソース管理は、特定の GitHub リポジトリにリンクされます。GitHub リポジトリは、**"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** コードによって定義されます。繰り返しデプロイされる単一のプロジェクトを、最小数のパラメーターを使用して作成する場合は、リポジトリの URL をハードコーディングできます。リポジトリの URL をハードコーディングする代わりに、リポジトリの URL 用のパラメーターを追加し、その値を **RepoUrl** プロパティで使用できます。[Web App with Web Jobs template (Web ジョフ゛がある Web アプリ用のテンプレート)](../app-service-web-deploy-web-app-with-webjobs.md)で、リポジトリ URL パラメーターの例を参照できます。

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 

<!---HONumber=Sept15_HO3-->