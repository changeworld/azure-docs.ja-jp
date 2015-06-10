<properties
   pageTitle="Azure リソース マネージャーのテンプレートの作成"
   description="宣言型 JSON 構文を使用して Azure にアプリケーションをデプロイする Azure リソース マネージャーのテンプレートを作成します。"
   services="multiple"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Azure リソース マネージャーのテンプレートの作成

Azure applications typically require a combination of resources (such as a database server, database, or website) to meet the desired goals.展開して、各リソースを別々 に管理するではなく、使用する Azure リソース マネージャーのテンプレートを配置およびプロビジョニングするすべての調整、1 つの操作で、アプリケーションのリソースを作成できます。テンプレートでは、アプリケーションのために必要なリソースを定義してさまざまな環境の値を入力するデプロイメントのパラメーターを指定します。テンプレートは、JSON、およびデプロイメントの値を構築する際の式で構成されます。

## テンプレートの形式

The following example shows the sections that make up the basic structure of a template.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Element name | 必須 | 説明
| :------------: | :------: | :----------
| $schema | あり | Location of the JSON schema file that describes the version of the template language.
| contentVersion | あり | Version of the template (such as 1.0.0.0).When deploying resources using the template, this value can be used to make sure that the right template is being used.
| 指定 | いいえ | リソースの展開をカスタマイズするのにはデプロイメントを実行すると、提供されている値です。
| variables | いいえ | Values that are used as JSON fragments in the template to simplify template language expressions.
| リソース | あり | Types of services that are deployed or updated in a resource group.
| outputs | いいえ | デプロイメント後に返される値。

We will examine the sections of the template in greater detail later in this topic.For now, we will review some of the syntax that makes up the template.

## Expressions and functions

The basic syntax of the template is JSON; however, expressions and functions extend the JSON that is available in the template and enable you to create values that are not strict literal values.Expressions are enclosed with brackets ([ and ]), and are evaluated when the template is deployed.Expressions can appear anywhere in a JSON string value and always return another JSON value.If you need to use a literal string that starts with a bracket [, you must use two brackets [[.通常、関数と式を使用してデプロイメントを構成する操作を実行します。JavaScript の場合と同様に、関数呼び出しは functionName(arg1,arg2,arg3) の形式になります。プロパティの参照には、ドットと [index] 演算子を使用します。次に一般的な関数を示します。 - **parameters(parameterName)** デプロイメントの実行時に提供されるパラメーター値を返します。 - **variables(variableName)** テンプレートで定義される変数を返します。 - **concat(arg1,arg2,arg3,...)** 複数の文字列値を結合します。この関数では任意の数の引数を使用できます。 - **base64(inputString)** 入力文字列の base 64 表現を返します。 - **resourceGroup()** 現在のリソース グループを表す構造化オブジェクト (ID、name、location プロパティ) を返します。 - **resourceId([resourceGroupName], resourceType, resourceName1, [resourceName2]...)** リソースの一意の識別子を返します。別のリソース グループからリソースを取得する際に使用できます。次の例では、いくつかの関数を使用して値を構築する方法を示しています。"variables": { "location": "[resourceGroup().location]", "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]", "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]" } 式と関数に関するここまでの知識でも、テンプレートのセクションを理解するには十分です。パラメーター、返される値の形式など、すべてのテンプレート関数の詳細については、「Azure リソース マネージャーのテンプレートの関数」をご覧ください。## パラメーター テンプレートのパラメーター セクションでは、リソースをデプロイするときにユーザーが入力できる値を指定します。テンプレート全体でこれらのパラメーター値を使用して、デプロイ済みのリソースに値を設定できます。テンプレートの他のセクションで使用できるのは、パラメーター セクションで宣言されるパラメーターだけです。パラメーター セクション内では、パラメーター値を使用して別のパラメーター値は構築できません。その種類の操作は通常、変数のセクションで発生します。次の構造のパラメーターを定義します。"parameters": { "<parameterName>" : { "type" : "<type-of-parameter-value>", "defaultValue": "<optional-default-value-of-parameter>", "allowedValues": [ "<optional-array-of-allowed-values>" ] } } | 要素名 | 必須 | 説明 | :------------: | :------: | :---------- | parameterName | はい | パラメーターの名前。有効な JavaScript ID。 | type | はい | パラメーター値の型。下記の許可される型のリストをご覧ください。 | defaultValue | いいえ | このパラメーターに値が入力されない場合のパラメーターの規定値。 | allowedValues | いいえ | 適切な値が確実に入力されるように、パラメーターに許可される値の配列。許可される型と値は次のとおりです。 - 文字列または secureString - 有効な JSON 文字列 - int - 有効な JSON 整数 - ブール - 有効な JSON ブール値 - オブジェクト - 有効な JSON オブジェクト - 配列 - 有効な JSON 配列 >[AZURE.NOTE] すべてのパスワード、キー、その他のシークレットで secureString 型を使用する必要があります。SecureString 型を持つテンプレート パラメーターをリソース デプロイメントの後に読み取ることはできません。次の例では、パラメーターの定義方法を示しています。"parameters": { "siteName": { "type": "string" }, "siteLocation": { "type": "string" }, "hostingPlanName": { "type": "string" }, "hostingPlanSku": { "type": "string", "allowedValues": [ "Free", "Shared", "Basic", "Standard", "Premium" ], "defaultValue": "Free" } } ## 変数 変数のセクションでは、テンプレート言語の式を簡略化するために使用できる値を構築します。通常、これらの変数は、パラメーターから提供される値に基づきます。次の例では、次の 2 つのパラメーター値から構築される変数の定義方法を示しています。"parameters": { "username": { "type": "string" }, "password": { "type": "secureString" } }, "variables": { "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]" } 次の例では、JSON の複合型である変数と、その他の変数から構築される変数を示します。 "parameters": { "environmentName": { "type": "string", "allowedValues": [ "test", "prod" ] } }, "variables": { "environmentSettings": { "test": { "instancesSize": "Small", "instancesCount": 1 }, "prod": { "instancesSize": "Large", "instancesCount": 4 } }, "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]", "instancesSize": "[variables('currentEnvironmentSettings').instancesSize", "instancesCount": "[variables('currentEnvironmentSettings').instancesCount" }

## リソース

In the resources section, you define the resources are deployed or updated.

You define resources with the following structure:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Element name | 必須 | 説明
| :----------------------: | :------: | :----------
| apiVersion | あり | Version of the API that supports the resource.
| type | あり | Type of the resource.This value is a combination of the namespace of the resource provider and the resource type that the resource provider supports.
| name | あり | Name of the resource.The name must follow URI component restrictions defined in RFC3986.
| location | いいえ | Supported geo-locations of the provided resource.
| tags | いいえ | Tags that are associated with the resource.
| dependsOn | いいえ | Resources that the resource being defined depends on.The dependencies between resources are evaluated and resources are deployed in their dependent order.When resources are not dependent on each other, they are attempted to be deployed in parallel.The value can be a comma separated list of a resource names or resource unique identifiers.
| プロパティ | いいえ | Resource specific configuration settings.
| リソース | いいえ | Child resources that depend on the resource being defined.

If the resource name is not unique, you can use the **resourceId** helper function (described below) to get the unique identifier for any resource.

The following example shows a **Microsoft.Web/serverfarms** resource and a **Microsoft.Web/sites** resource with a nested **Extensions** resource:

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## Outputs

[出力] セクションではデプロイメントから返される値を指定します。For example, you could return the URI to access a deployed resource.

The following example shows the structure of an output definition:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Element name | 必須 | 説明
| :------------: | :------: | :----------
| outputName | あり | Name of the output value.Must be a valid JavaScript identifier.
| type | あり | Type of the output value.Output values support the same types as template input parameters.
| 値 | あり | Template language expression which will be evaluated and returned as output value.


The following example shows a value that is returned in the Outputs section.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## More advanced scenarios.
This topic provides an introductory look at the template.However, your scenario may require more advanced tasks.

You may need to merge two templates together or use a child template within a parent template.For more information, see [Nested Templates](../resource-group-advanced-template#nested-template).

You may need to use resources that exist within a different resource group.This is common when working with storage accounts or virtual networks that are shared across multiple resource groups.For more information, see the [resourceId function](../resource-group-template-functions#resourceid).

## Complete template
The following template deploys a web app and provisions it with code from a .zip file.

    {
       "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## 次のステップ
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)
- [高度なテンプレートの操作](./resource-group-advanced-template.md)
- [Azure リソース マネージャーの概要](./resource-group-overview.md)

<!---HONumber=58-->