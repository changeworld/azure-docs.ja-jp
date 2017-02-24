---
title: "Azure デプロイ用テンプレートの作成 | Microsoft Docs"
description: "宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 52fe8e3ce0c9c94c918818784fd735b5a6486ed8


---
# <a name="authoring-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートの作成
このトピックでは、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 

既にデプロイしているリソースのテンプレートを表示するには、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」をご覧ください。 テンプレートの作成に関するガイダンスについては、「 [Resource Manager テンプレートのチュートリアル](resource-manager-template-walkthrough.md)」をご覧ください。 テンプレート作成の推奨事項については、「 [Azure Resource Manager テンプレートを作成するためのベスト プラクティス](resource-manager-template-best-practices.md)」を参照してください。

テンプレートを作成する作業は、適切な JSON エディターを使用することで省力化できます。 テンプレートでの Visual Studio の使用の詳細については、「 [Visual Studio での Azure リソース グループの作成とデプロイ](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。 VS Code の使用については、「 [Visual Studio Code で Azure Resource Manager テンプレートを操作する](resource-manager-vs-code.md)」を参照してください。

テンプレートのサイズを 1 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 1 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 

## <a name="template-format"></a>テンプレートの形式
最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

| 要素名
 | 必須 | 説明 |
|:--- |:--- |:--- |
| $schema |はい |テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。 前の例に示されている URL を使用します。 |
| contentVersion |はい |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| parameters |いいえ |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| variables |いいえ |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| resources |はい |リソース グループ内でデプロイまたは更新されるリソースの種類。 |
| outputs |いいえ |デプロイ後に返される値。 |

テンプレートのセクションについては、後で詳しく説明します。

## <a name="expressions-and-functions"></a>式と関数

テンプレートの基本的な構文は JSON です。 ただし、式や関数により、テンプレートで使用できる JSON が拡張されます。 式を使用すると、厳密なリテラル値以外の値を作成できます。 式は角かっこ (`[` と `]`) で囲まれ、テンプレートがデプロイされるときに評価されます。 式は、JSON 文字列値内の任意の場所に配置でき、常に別の JSON 値を返します。 角かっこ `[` で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ `[[` を使用する必要があります。

通常、関数と式を使用してデプロイを構成する操作を実行します。 JavaScript の場合と同様に、関数呼び出しは **functionName(arg1,arg2,arg3)**という形式になります。 プロパティの参照には、ドットと [index] 演算子を使用します。

次の例では、値を構築する際にいくつかの関数を使用する方法を示します。

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。 

## <a name="parameters"></a>parameters
テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 特定の環境 (開発、テスト、運用など) に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 テンプレートでは必ずしもパラメーターを使用する必要はありませんが、パラメーターを使わなかった場合、常に同じリソースが同じ名前、同じ場所、同じプロパティでデプロイされます。

テンプレート全体でこれらのパラメーター値を使用して、デプロイ済みのリソースに値を設定できます。 テンプレートの他のセクションで使用できるのは、パラメーター セクションで宣言されるパラメーターだけです。

次の構造でパラメーターを定義します。

```json
"parameters": {
   "<parameter-name>" : {
     "type" : "<type-of-parameter-value>",
     "defaultValue": "<default-value-of-parameter>",
     "allowedValues": [ "<array-of-allowed-values>" ],
     "minValue": <minimum-value-for-int>,
     "maxValue": <maximum-value-for-int>,
     "minLength": <minimum-length-for-string-or-array>,
     "maxLength": <maximum-length-for-string-or-array-parameters>,
     "metadata": {
         "description": "<description-of-the parameter>" 
     }
   }
}
```

| 要素名
 | 必須 | Description |
|:--- |:--- |:--- |
| parameterName |はい |パラメーターの名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |パラメーター値の型。 この表の後に示す使用できる型を参照してください。 |
| defaultValue |いいえ |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ |int 型パラメーターの最小値。 |
| maxValue |いいえ |int 型パラメーターの最大値。 |
| minLength |いいえ |文字列型、secureString 型、配列型パラメーターの長さの最小値。 |
| maxLength |いいえ |文字列型、secureString 型、配列型パラメーターの長さの最大値。 |
| description |なし |ポータル カスタム テンプレート インターフェイス経由でテンプレートのユーザーに表示されるパラメーターの説明。 |

使用できる型と値は次のとおりです。

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

パラメーターを省略可能に指定するには、defaultValue を設定します (空の文字列を指定できます)。 

コマンドのパラメーターと同じ名前のパラメーターをテンプレートで指定して、そのテンプレートをデプロイすると、指定する値があいまいになる可能性があります。 Resource Manager では、接尾辞 **FromTemplate** をテンプレート パラメーターに追加することで、このような混乱を防ぎます。 たとえば、**ResourceGroupName** という名前のパラメーターをテンプレートに追加した場合、このパラメーターは、[New-AzureRmResourceGroupDeployment][deployment2cmdlet] コマンドレットの **ResourceGroupName** パラメーターと競合するため、 デプロイ中、**ResourceGroupNameFromTemplate** に値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

> [!NOTE]
> すべてのパスワード、キー、およびその他のシークレットでは、 **secureString** 型を使用する必要があります。 JSON オブジェクトに機密データを渡す場合は、**secureObject** 型を使用します。 secureString 型または secureObject 型を含むテンプレート パラメーターをリソースのデプロイ後に読み取ることはできません。 
> 
> たとえば、デプロイ履歴の次のエントリには文字列とオブジェクトの値が表示されますが、secureString と secureObject の値は表示されません。
>
> ![デプロイの値の表示](./media/resource-group-authoring-templates/show-parameters.png)  
>

次の例では、パラメーターを定義する方法を示します。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  },
  "skuName": {
    "type": "string",
    "defaultValue": "F1",
    "allowedValues": [
      "F1",
      "D1",
      "B1",
      "B2",
      "B3",
      "S1",
      "S2",
      "S3",
      "P1",
      "P2",
      "P3",
      "P4"
    ]
  },
  "skuCapacity": {
    "type": "int",
    "defaultValue": 1,
    "minValue": 1
  }
}
```

デプロイ時にパラメーター値を入力する方法については、 [Azure Resource Manager テンプレートを使用したリソースのデプロイ](resource-group-template-deploy.md)に関するページをご覧ください。 

## <a name="variables"></a>variables
テンプレート内で使用できる値は、variables セクションで構築します。 通常、変数は、パラメーターから提供される値に基づきます。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

変数は、次の構造で定義します。

```json
"variables": {
   "<variable-name>": "<variable-value>",
   "<variable-name>": { 
       <variable-complex-type-value> 
   }
}
```

次の例では、2 つのパラメーター値で構成される変数の定義方法を示しています。

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

次の例では、複雑な JSON 型である変数と、その他の変数で構成される変数を示します。

```json
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
   "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
   "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>リソース
resources セクションでは、デプロイまたは更新されるリソースを定義します。 このセクションは、複雑になりやすい部分です。適切な値を指定するためには、デプロイするリソースの種類を理解している必要があるためです。 

リソースは、次の構造で定義します。

```json
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
     "copy": {
       "name": "<name-of-copy-loop>",
       "count": "<number-of-iterations>"
     },
     "resources": [
       "<array-of-child-resources>"
     ]
   }
]
```

| 要素名
 | 必須 | 説明 |
|:--- |:--- |:--- |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間と、リソースの種類の組み合わせです (例: **Microsoft.Storage/storageAccounts**)。 |
| name |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 また、リソース名を外部に公開する Azure サービスは、名前が別の ID になりすますことがないように、その名前を検証します。 「 [Check resource name (リソース名を確認する)](https://msdn.microsoft.com/library/azure/mt219035.aspx)」をご覧ください。 |
| location |多様 |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 |
| tags |いいえ |リソースに関連付けられたタグ。 |
| コメント |いいえ |テンプレート内にドキュメント化するリソースについてのメモ。 |
| dependsOn |なし |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 詳細については、[Azure Resource Manager テンプレートの依存関係の定義](resource-group-define-dependencies.md)に関するページをご覧ください。 |
| properties |いいえ |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 リソース スキーマのドキュメントまたは REST API へのリンクについては、[Resource Manager のプロバイダー、リージョン、API のバージョン、およびスキーマ](resource-manager-supported-services.md)に関するページをご覧ください。 |
| copy |いいえ |複数のインスタンスが必要な場合に作成するリソースの数。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 |
| resources |いいえ |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 子リソースの完全修飾型には親リソースの種類が含まれます (例: **Microsoft.Web/sites/extensions**)。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 |

**apiVersion**、**type**、**location** に指定する値は、すぐには分かりません。 さいわいなことに、Azure PowerShell または Azure CLI からこれらの値を特定できます。

**PowerShell** ですべてのリソース プロバイダーを取得するには、次を使用します。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

返された一覧から、目的のリソース プロバイダーを見つけます。 リソース プロバイダー (ストレージなど) のリソースの種類を取得するには、次を使用します。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

リソースの種類 (ストレージ アカウントなど) の API バージョンを取得するには、次を使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

リソースの種類のサポートされている場所を取得するには、次を使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

**Azure CLI** ですべてのリソース プロバイダーを取得するには、次を使用します。

```azurecli
azure provider list
```

返された一覧から、目的のリソース プロバイダーを見つけます。 リソース プロバイダー (ストレージなど) のリソースの種類を取得するには、次を使用します。

```azurecli
azure provider show Microsoft.Storage
```

サポートされている場所と API のバージョンを取得するには、次を使用します。

```azurecli
azure provider show Microsoft.Storage --details --json
```

リソース プロバイダーの詳細については、[Resource Manager のプロバイダー、リージョン、API のバージョン、およびスキーマ](resource-manager-supported-services.md)に関するページをご覧ください。

resources セクションには、デプロイの対象となる一連のリソースが記述されます。 また、リソースごとに子リソースを複数定義することができます。 その場合、resources セクションは次のような構造となります。

```json
"resources": [
   {
       "name": "resourceA",
   },
   {
       "name": "resourceB",
       "resources": [
           {
               "name": "firstChildResourceB",
           },
           {   
               "name": "secondChildResourceB",
           }
       ]
   },
   {
       "name": "resourceC",
   }
]
```      

次の例では、**Microsoft.Web/serverfarms** リソースと、入れ子になった **Extensions** リソースを含む **Microsoft.Web/sites** リソースを示しています。 サイトがサーバー ファームに依存するリソースとして指定されている点に注意してください。これは、サーバー ファームが存在して初めてサイトをデプロイできるためです。 また、**Extensions** リソースがサイトの子になっている点にも注意してください。

```json
"resources": [
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('hostingPlanName')]",
    "type": "Microsoft.Web/serverfarms",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "HostingPlan"
    },
    "sku": {
      "name": "[parameters('skuName')]",
      "capacity": "[parameters('skuCapacity')]"
    },
    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }
  },
  {
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[parameters('siteName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "environment": "test",
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
    ],
    "properties": {
      "name": "[parameters('siteName')]",
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    },
    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "extensions",
        "name": "MSDeploy",
        "dependsOn": [
          "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
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
]
```

## <a name="outputs"></a>出力

[出力] セクションではデプロイから返される値を指定します。 たとえば、デプロイされたリソースにアクセスするための URI を返すことができます。

次の例では、出力の定義の構造を示します。

```json
"outputs": {
   "<outputName>" : {
     "type" : "<type-of-output-value>",
     "value": "<output-value-expression>"
   }
}
```

| 要素名
 | 必須 | 説明 |
|:--- |:--- |:--- |
| outputName |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 |
| 値 |はい |評価され、出力値として返されるテンプレート言語式。 |

次の例では、outputs セクションで返される値を示します。

```json
"outputs": {
   "siteUri" : {
     "type" : "string",
     "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
   }
}
```

出力を操作する方法の詳細については、「 [Azure Resource Manager のテンプレートでの状態の共有](best-practices-resource-manager-state.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* 別のリソース グループ内に存在するリソースの使用が必要になる場合があります。 このシナリオは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。 詳細については、 [resourceId 関数](resource-group-template-functions.md#resourceid)に関するセクションをご覧ください。

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment



<!--HONumber=Jan17_HO4-->


