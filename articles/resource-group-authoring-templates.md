<properties
   pageTitle="Azure リソース マネージャーのテンプレートの作成"
   description="宣言型 JSON 構文を使用して Azure にアプリケーションをデプロイする Azure リソース マネージャーのテンプレートを作成します。"
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
   ms.date="11/30/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートの作成

Azure アプリケーションでは、通常、目的を達成するためにリソース (データベース サーバー、データベース、Web サイトなど) を組み合わせる必要があります。  
デプロイして、各リソースを別々 に管理するではなく、使用する Azure リソース マネージャーのテンプレートをデプロイおよびプロビジョニングするすべての調整、1 つの操作で、アプリケーションのリソースを作成できます。テンプレートでは、アプリケーションのために必要なリソースを定義してさまざまな環境の値を入力するデプロイのパラメーターを指定します。テンプレートは、JSON、およびデプロイの値を構築する際の式で構成されます。

このトピックでは、テンプレートの各セクションについて説明します。実際のスキーマについては、[Azure リソース マネージャーのスキーマ](https://github.com/Azure/azure-resource-manager-schemas)に関するページを参照してください。Visual Studio には、テンプレートの作成を支援するためのツールが用意されています。テンプレートでの Visual Studio の使用の詳細については、「[Azure リソース グループ デプロイ プロジェクトの作成とデプロイ](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」および「[Azure リソース グループへのリソースの追加](vs-azure-tools-resource-group-adding-resources.md)」を参照してください。

テンプレートのサイズを 1 MB に、各パラメーター ファイルのサイズを 64 KB に制限する必要があります。1 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。

## テンプレートの形式

次の例では、テンプレートの基本構造を構成するセクションを示します。  


    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| 要素名   | 必須 | 説明
| :------------: | :------: | :----------
| $schema        |   あり    | テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。 
| contentVersion |   あり    | テンプレートのバージョン (1.0.0.0 など)。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。
| parameters     |   いいえ     | リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。
| variables      |   いいえ     | テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。
| リソース      |   あり    | リソース グループ内でデプロイまたは更新されるサービスの種類。
| outputs        |   いいえ     | デプロイ後に返される値。

テンプレートのセクションについては、後で詳しく説明します。  
次に、テンプレートを構成する構文のいくつかを確認します。  


## 式と関数  


テンプレートの基本的な構文は JSON ですが、式や関数を使用すると、テンプレートで使用できる JSON が拡張され、厳密なリテラル値以外の値を作成できるようになります。  
式は角かっこ ([ と ]) で囲まれ、テンプレートがデプロイされるときに評価されます。式は、JSON 文字列値内の任意の場所に配置でき、常に別の JSON 値を返します。  
角かっこ [ で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ [[ を使用する必要があります。

通常、関数と式を使用してデプロイを構成する操作を実行します。JavaScript の場合と同様に、関数呼び出しは **functionName(arg1,arg2,arg3)** という形式になります。プロパティの参照には、ドットと [index] 演算子を使用します。

次の例では、値を構築する際にいくつかの関数を使用する方法を示します。
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

テンプレート関数の完全一覧が必要な場合、「[Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)」を参照してください。


## パラメーター

テンプレートの parameters セクションでは、ユーザーがリソースをデプロイする際に入力できる値を指定します。テンプレート全体でこれらのパラメーター値を使用して、デプロイ済みのリソースに値を設定できます。テンプレートの他のセクションで使用できるのは、パラメーター セクションで宣言されるパラメーターだけです。

パラメーター セクション内では、パラメーター値を使用して別のパラメーター値は構築できません。その種類の操作は通常、変数のセクションで発生します。

次の構造でパラメーターを定義します。

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| 要素名 | 必須 | 説明
| :------------: | :------: | :----------
| parameterName | あり | パラメーターの名前。有効な JavaScript 識別子で指定する必要があります。
| type | あり | パラメーター値の型。使用できる型については、下にある一覧を参照してください。
| defaultValue | いいえ | パラメーターに値が指定されない場合のパラメーターの既定値。
| allowedValues | いいえ | 適切な値が確実に指定されるように、パラメーターに使用できる値の配列。
| minValue | いいえ | int 型パラメーターの最小値。
| maxValue | いいえ | int 型パラメーターの最大値。
| minLength | いいえ | 文字列型、secureString 型、配列型パラメーターの長さの最小値。
| maxLength | いいえ | 文字列型、secureString 型、配列型パラメーターの長さの最大値。
| description | いいえ | ポータル カスタム テンプレート インターフェイス経由でテンプレートの利用者に表示されるパラメーターの説明。

使用できる型と値は次のとおりです。

- string または secureString - 有効な JSON 文字列
- int - 有効な JSON 整数
- bool - 有効な JSON ブール値
- object または secureObject - 有効な JSON オブジェクト
- array - 有効な JSON 配列

パラメーターを省略可能に指定するには、defaultValue を空の文字列に設定します。

>[AZURE.NOTE]すべてのパスワード、キー、およびその他のシークレットでは、**secureString** 型を使用する必要があります。SecureString 型を持つテンプレート パラメーターをリソース デプロイの後に読み取ることはできません。

次の例では、パラメーターを定義する方法を示します。

    "parameters": {
       "siteName": {
          "type": "string",
          "minLength": 2,
          "maxLength": 60
       },
       "siteLocation": {
          "type": "string",
          "minLength": 2
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
       },
       "instancesCount": {
          "type": "int",
          "maxValue": 10
       },
       "numberOfWorkers": {
          "type": "int",
          "minValue": 1
       }
    }

## 変数

variables セクションでは、テンプレート言語式を簡略化するために使用できる値を構築します。通常、これらの変数は、パラメーターから提供される値に基づきます。

変数は、次の構造で定義します。

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

次の例では、2 つのパラメーター値で構成される変数の定義方法を示しています。

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

次の例では、複雑な JSON 型である変数と、その他の変数で構成される変数を示します。

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## リソース

resources セクションでは、デプロイまたは更新されるリソースを定義します。  


リソースは、次の構造で定義します。  


    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| 要素名 | 必須 | 説明
| :----------------------: | :------: | :----------
| apiVersion | あり | リソースの作成に使用する REST API バージョン。特定のリソース タイプの使用できるバージョン番号を特定するには、「[サポートされている API バージョン](../resource-manager-supported-services/#supported-api-versions)」を参照してください。
| type | あり | リソースの種類。  この値は、リソース プロバイダーの名前空間と、リソース プロバイダーがサポートするリソースの種類の組み合わせです。  

| name | あり | リソースの名前。  この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。  

| location | いいえ | 指定されたリソースのサポートされている地理的な場所。  

| tags | いいえ | リソースに関連付けられたタグ。  

| コメント | いいえ | テンプレート内にドキュメント化するリソースについてのメモ。
| dependsOn | いいえ | 定義されているリソースが依存するリソース。  リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。  相互依存していないリソースは、平行してデプロイされます。  値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。  

| プロパティ | いいえ | リソース固有の構成設定。  

| リソース | いいえ | 定義されているリソースに依存する子リソース。  


リソース名が一意でない場合は、**resourceId** ヘルパー関数 (後で説明) を使用すると、リソースの一意識別子を取得できます。

**properties** 要素の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値とまったく同じです。デプロイするリソースの REST API 操作については、「[Azure リファレンス](https://msdn.microsoft.com/library/azure/mt420159.aspx)」を参照してください。

次の例では、**Microsoft.Web/serverfarms** リソースと、入れ子になった **Extensions** リソースを含む **Microsoft.Web/sites** リソースを示しています。

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


## 出力  


[出力] セクションではデプロイから返される値を指定します。たとえば、デプロイされたリソースにアクセスするための URI を返すことができます。  


次の例では、出力の定義の構造を示します。  


    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| 要素名 | 必須 | 説明
| :------------: | :------: | :----------
| outputName | あり | 出力値の名前。  有効な JavaScript 識別子で指定する必要があります。  

| type | あり | 出力値の型。  出力値では、テンプレート入力パラメーターと同じ型がサポートされています。  

| 値 | あり | 評価され、出力値として返されるテンプレート言語式。  



次の例では、outputs セクションで返される値を示します。  


    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## より高度なシナリオ  

このトピックでは、テンプレートの概要を示します。  
ただし、シナリオによっては、より高度なタスクが必要になる場合があります。  


2 つのテンプレートをマージしたり、親テンプレート内で子テンプレートを使用したりすることが必要な場合があります。詳細については、「[Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

1 種類のリソースを指定した回数分繰り返し作成するには、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

別のリソース グループ内に存在するリソースの使用が必要になる場合があります。  
これは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。  
詳細については、「[resourceId 関数](../resource-group-template-functions#resourceid)」を参照してください。

## 完全なテンプレート  

次に、Web アプリをデプロイし、.zip ファイルのコードを使用してそのアプリをプロビジョニングするテンプレートを示します。  


    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
- テンプレート内から使用できる関数の詳細については、「[Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。
- 作成したテンプレートをデプロイする方法を確認するには、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。
- アプリケーションのデプロイの詳細な例については、「[Azure でマイクロサービスを予測どおりにデプロイする](app-service-web/app-service-deploy-complex-application-predictably.md)」を参照してください。
- 使用可能なスキーマを確認するには、[Azure リソース マネージャーのスキーマ](https://github.com/Azure/azure-resource-manager-schemas)に関するページを参照してください。

<!---HONumber=AcomDC_1203_2015-->