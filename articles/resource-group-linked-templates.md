<properties
   pageTitle="Resource Manager でのリンクされたテンプレート | Microsoft Azure"
   description="Azure リソース マネージャー テンプレートでリンクされたテンプレートを使用して、モジュール構造のテンプレート ソリューションを作成する方法について説明します。パラメーターの値を渡す方法、パラメーター ファイルを指定する方法、および URL を動的に作成する方法を示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでのリンクされたテンプレートの使用

1 つの Azure リソース マネージャー テンプレート内から別のテンプレートにリンクして、対象となる、目的に特化した一連のテンプレートにデプロイを分解することができます。アプリケーションを複数のコード クラスに分解する場合と同様に、分解すると、テスト、再利用、読みやすさの面でメリットがあります。

メイン テンプレートからリンクされたテンプレートにパラメーターを渡すことができます。それらのパラメーターは、呼び出し元のテンプレートによって公開されているパラメーターまたは変数に直接マップできます。リンクされたテンプレートからソース テンプレートに出力変数を渡すこともできます。そのため、テンプレート間で双方向のデータ交換を行うことができます。

## テンプレートへのリンク

2 つのテンプレート間のリンクを作成するには、リンク先のテンプレートを指すデプロイ リソースをメイン テンプレート内に追加します。**templateLink** プロパティを、リンク先のテンプレートの URI に設定します。リンクされたテンプレートのパラメーター値を指定するには、テンプレート内で値を直接指定するか、パラメーター ファイルにリンクします。次の例では、**parameters** プロパティを使用して、パラメーター値を直接指定しています。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

リソース マネージャー サービスはリンクされたテンプレートにアクセスできる必要があります。このため、リンクされたテンプレートには、ローカル ファイルまたはローカル ネットワーク上でのみ使用できるファイルは指定できません。**http** または **https** のいずれかを含む URI 値のみを指定できます。オプションの 1 つとして、ストレージ アカウントにリンク先のテンプレートを配置し、次に示すように、その項目の URI を使用します。

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

リンクされたテンプレートは外部から利用可能でなければなりませんが、一般公開する必要はありません。ストレージ アカウント所有者のみがアクセスできるプライベート ストレージ アカウントにテンプレートを追加し、デプロイ時にアクセスを有効にするため Shared Access Signature (SAS) トークンを作成することができます。リンクされたテンプレートの URI に SAS トークンを追加します。ストレージ アカウントにテンプレートを設定し SAS トークンを生成する手順については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](resource-group-template-deploy.md)」または「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](resource-group-template-deploy-cli.md)」を参照してください。

次の例は、別のテンプレートにリンクしている親テンプレートを示しています。入れ子になったテンプレートには、パラメーターとして渡された SAS トークンを使用してアクセスします。

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

トークンがセキュリティで保護された文字列として渡された場合でも、SAS トークンを含むリンクされたテンプレートの URI が、リソース グループのデプロイ操作にログ記録されます。公開を制限するには、トークンの有効期限を設定します。

## パラメーター ファイルへのリンク

次の例では、 **parametersLink** プロパティを使用して、パラメーター ファイルにリンク付けしています。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

リンクされたパラメーター ファイルの URI 値はローカル ファイルにすることはできず、**http** または **https** のいずれかを含む必要があります。もちろん、パラメーター ファイルは SAS トークンを使ったアクセスに制限することができます。

## 変数を使用したテンプレートのリンク

前の例では、URL の値をハード コーディングしてテンプレートをリンクする方法について説明しました。この方法は簡単なテンプレートには適していますが、モジュール構造の大規模な一連のテンプレートを使用する場合にはあまり適していません。その場合は、メイン テンプレートのベース URL を格納する静的変数を作成し、リンクされたテンプレートの URL をそのベース URL から動的に作成することができます。この方法の利点としては、テンプレートを簡単に移動したり、フォークしたりできることが挙げられます。メイン テンプレート内の静的変数を変更するだけで、正しい URI が、メイン テンプレートから、分解されたテンプレート全体に渡されます。

次の例では、ベース URL を使用して、リンクされたテンプレート (**sharedTemplateUrl** と **vmTemplate**) の 2 つの URL を作成する方法を示しています。

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

[deployment()](resource-group-template-functions.md#deployment) を使用して、現在のテンプレートのベース URL を取得したり、同じ場所にある他のテンプレートの URL を取得したりすることもできます。これは、テンプレートの場所が変更された場合や (バージョン管理などのため)、テンプレート ファイルのハード コーディング URL を回避する必要がある場合に便利です。

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## 完全な例

次のサンプル テンプレートは、この記事で取り上げている概念を説明するために、リンクされたテンプレートの簡略化した配置を示しています。パブリック アクセスを無効にした状態で、テンプレートがストレージ アカウント内の同じコンテナーに追加されていることを前提としています。リンクされたテンプレートは **outputs** セクションのメイン テンプレートに値を渡します。

**parent.json** ファイルの構成は次のとおりです。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('nestedTemplate').outputs.result]"
        }
      }
    }

**helloworld.json** ファイルの構成は次のとおりです。

    {
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {},
	  "variables": {},
	  "resources": [],
	  "outputs": {
		"result": {
			"value": "Hello World",
			"type" : "string"
		}
	  }
    }
    
PowerShell では、コンテナーのトークンを取得し、以下を使用してテンプレートをデプロイします。

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Azure CLI では、コンテナーのトークンを取得し、次のコードを使用してテンプレートをデプロイします。現時点では、SAS トークンを含むテンプレート URI を使用する場合は、デプロイの名前を指定する必要があります。

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

パラメーターとして SAS トークンを指定するように求められます。トークンの前に **?** をつける必要があります。

## 次のステップ
- リソースのデプロイの順序の定義については、「[Azure Resource Manager テンプレートでの依存関係の定義](resource-group-define-dependencies.md)」をご覧ください。
- リソースを 1 つ定義し、そのリソースの複数のインスタンスを作成する方法については、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

<!---HONumber=AcomDC_0615_2016-->