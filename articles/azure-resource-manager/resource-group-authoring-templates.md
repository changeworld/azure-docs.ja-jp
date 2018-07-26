---
title: Azure Resource Manager テンプレートの構造と構文 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: tomfitz
ms.openlocfilehash: e1964b7f46259e54c65aeb46aa795713922c3504
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114614"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構造と構文の詳細
この記事では、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 テンプレートの作成方法を詳しく解説したチュートリアルについては、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」を参照してください。

## <a name="template-format"></a>テンプレートの形式
最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| $schema |[はい] |テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。 前の例に示されている URL を使用します。 |
| contentVersion |[はい] |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 この値を使用し、テンプレートの大きな変更を記述します。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| parameters |いいえ  |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| variables |いいえ  |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| functions |いいえ  |テンプレート内で使用できるユーザー定義関数。 |
| resources |[はい] |リソース グループ内でデプロイまたは更新されるリソースの種類。 |
| outputs |いいえ  |デプロイ後に返される値。 |

各要素には、設定できるプロパティがあります。 次の例には、テンプレートの完全な構文があります。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

この記事では、テンプレートのセクションについて詳しく説明します。

## <a name="syntax"></a>構文
テンプレートの基本的な構文は JSON です。 ただし、式や関数により、テンプレートで使用できる JSON 値が拡張されます。  式は、最初と最後の文字が角かっこ (`[` および `]`) の JSON 文字列リテラル内に記述されます。 式の値は、テンプレートのデプロイ時に評価されます。 文字列リテラルとして記述される一方で、式の評価の結果は、実際の式に応じて、配列、整数など、さまざまな JSON 型にすることができます。  角かっこ `[` で始まるリテラル文字列を使用するが、式として解釈されないようにするには、文字列が `[[` で始まるように追加の角かっこを追加します。

通常、関数と式を使用してデプロイを構成する操作を実行します。 JavaScript の場合と同様に、関数呼び出しは `functionName(arg1,arg2,arg3)` という形式になります。 プロパティの参照には、ドットと [index] 演算子を使用します。

次の例では、値を構築する際にいくつかの関数を使用する方法を示します。

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。 

## <a name="parameters"></a>parameters
テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 特定の環境 (開発、テスト、運用など) に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 テンプレートでは必ずしもパラメーターを使用する必要はありませんが、パラメーターを使わなかった場合、常に同じリソースが同じ名前、同じ場所、同じプロパティでデプロイされます。

単純なパラメーター定義の例を次に示します。

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

パラメーター定義の詳細については、「[Azure Resource Manager テンプレートの parameters セクション](resource-manager-templates-parameters.md)」をご覧ください。

## <a name="variables"></a>variables
テンプレート内で使用できる値は、variables セクションで構築します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

単純な変数定義の例を次に示します。

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

変数定義の詳細については、「[Azure Resource Manager テンプレートの変数セクション](resource-manager-templates-variables.md)」をご覧ください。

## <a name="functions"></a>Functions

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 通常は、テンプレート内で繰り返したくない複雑な式を定義します。 ユーザー定義関数は、テンプレートでサポートされている[関数](resource-group-template-functions.md)および式から作成します。

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、テンプレート パラメーターにアクセスできません。 つまり、[パラメーター関数](resource-group-template-functions-deployment.md#parameters)は、関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数は [reference 関数](resource-group-template-functions-resource.md#reference)を使用できません。
* 関数のパラメーターでは既定値を指定できません。

テンプレート関数との名前の競合を回避するために、お使いの関数には名前空間の値が必要です。 次の例は、ストレージ アカウント名を返す関数を示しています。

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

関数を呼び出すには、次を使用します。

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>リソース
resources セクションでは、デプロイまたは更新されるリソースを定義します。 このセクションは、複雑になりやすい部分です。適切な値を指定するためには、デプロイするリソースの種類を理解している必要があるためです。

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

詳細については、「[Resources section of Azure Resource Manager templates (Azure Resource Manager テンプレートの resources セクション)](resource-manager-templates-resources.md)」をご覧ください。

## <a name="outputs"></a>出力
[出力] セクションではデプロイから返される値を指定します。 たとえば、デプロイされたリソースにアクセスするための URI を返すことができます。

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

詳細については、「[Outputs section of Azure Resource Manager templates (Azure Resource Manager テンプレートの outputs セクション)](resource-manager-templates-outputs.md)」をご覧ください。

## <a name="template-limits"></a>テンプレートの制限

テンプレートのサイズを 1 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 1 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 

また、以下のように制限されます。

* パラメーター 256 個
* 変数 256 個
* リソース (コピー数を含む) 800 個
* 出力値 64 個
* テンプレート式内で 24,576 文字

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](resource-manager-objects-as-parameters.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* グローバル Azure、Azure ソブリン クラウド、Azure Stack で使用できる Resource Manager テンプレートの作成に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」をご覧ください。
