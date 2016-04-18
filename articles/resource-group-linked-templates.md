<properties
   pageTitle="Azure Resource Manager でのリンクされたテンプレート | Microsoft Azure"
   description="Azure リソース マネージャー テンプレートでリンクされたテンプレートを使用して、モジュール構造のテンプレート ソリューションを作成する方法について説明します。パラメーターの値を渡す方法、パラメーター ファイルを指定する方法、および URL を動的に作成する方法を示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/04/2016"
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

リンクされたパラメーター ファイルの URI 値はローカル ファイルにすることはできず、**http** または **https** のいずれかを含む必要があります。

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

[deployment()](../resource-group-template-functions/#deployment) を使用して、現在のテンプレートのベース URL を取得したり、同じ場所にある他のテンプレートの URL を取得したりすることもできます。これは、テンプレートの場所が変更された場合や (バージョン管理などのため)、テンプレート ファイルのハード コーディング URL を回避する必要がある場合に便利です。

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## リンクされたテンプレートから値を渡す

リンクされたテンプレートからメイン テンプレートに値を渡す必要がある場合は、リンクされたテンプレートの **outputs** セクションで値を作成できます。例については、「[Azure リソース マネージャーのテンプレートでの状態の共有](best-practices-resource-manager-state.md)」を参照してください。

## 次のステップ
- リソースのデプロイの順序の定義については、「[Azure Resource Manager テンプレートでの依存関係の定義](resource-group-define-dependencies.md)」をご覧ください。
- リソースを 1 つ定義し、そのリソースの複数のインスタンスを作成する方法については、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

<!---HONumber=AcomDC_0406_2016-->