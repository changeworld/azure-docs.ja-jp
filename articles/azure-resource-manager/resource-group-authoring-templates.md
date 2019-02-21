---
title: Azure Resource Manager テンプレートの構造と構文 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 34f34545e4511c4f8bc4af95f906f2871480bd47
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310162"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構造と構文の詳細

この記事では、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 テンプレートの作成方法を詳しく解説したチュートリアルについては、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」を参照してください。

## <a name="template-format"></a>テンプレートの形式

最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| $schema |はい |テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。<br><br> リソース グループ デプロイの場合、次を使用します。`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>サブスクリプション デプロイの場合、次を使用します。`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |はい |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 この値を使用し、テンプレートの大きな変更を記述します。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| parameters |いいえ  |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| variables |いいえ  |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| functions |いいえ  |テンプレート内で使用できるユーザー定義関数。 |
| resources |はい |リソース グループまたはサブスクリプション内でデプロイまたは更新されるリソースの種類。 |
| outputs |いいえ  |デプロイ後に返される値。 |

各要素には、設定できるプロパティがあります。 次の例には、テンプレートの完全な構文があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                    "input": <object-or-value-to-repeat>
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
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
            "condition": "<boolean-value-whether-to-output-value>",
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
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で[パラメーター関数](resource-group-template-functions-deployment.md#parameters)を使用する場合、その関数のパラメーターに制限されます。
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

デプロイ時に条件付きでリソースを含めたり除外したりするには、[Condition 要素](resource-manager-templates-resources.md#condition)を使用します。 resources セクションの詳細については、「[Resources section of Azure Resource Manager templates (Azure Resource Manager テンプレートの resources セクション)](resource-manager-templates-resources.md)」をご覧ください。

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

<a id="comments" />

## <a name="comments-and-metadata"></a>コメントとメタデータ

テンプレートにコメントとメタデータを追加するためのオプションがいくつかあります。

`metadata` オブジェクトは、テンプレート内のほとんどどこにでも追加できます。 このオブジェクトは、Resource Manager では無視されますが、お使いの JSON エディターによっては、プロパティが無効であると警告される場合があります。 オブジェクトで、必要なプロパティを定義します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "comments": "This template was developed for demonstration purposes.",
        "author": "Example Name"
    },
```

**パラメーター**の場合、`description` プロパティを使って `metadata` オブジェクトを追加します。

```json
"parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "User name for the Virtual Machine."
      }
    },
```

ポータルからテンプレートをデプロイする場合、説明で指定したテキストがそのパラメーターのヒントとして自動的に使用されます。

![パラメーター ヒントの表示](./media/resource-group-authoring-templates/show-parameter-tip.png)

**リソース**の場合、`comments` 要素またはメタデータ オブジェクトを追加します。 次の例は、コメント要素とメタデータ オブジェクトの両方を示しています。

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**出力**の場合、メタデータ オブジェクトを出力値に追加します。

```json
"outputs": {
    "hostname": {
      "type": "string",
      "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
      "metadata": {
        "comments": "Return the fully qualified domain name"
      }
    },
```

メタデータ オブジェクトをユーザー定義関数に追加することはできません。

インライン コメントの場合、`//` を使用できますが、この構文はすべてのツールで機能しません。 Azure CLI を使用してインライン コメントを使用したテンプレートをデプロイすることはできません。 また、インライン コメントを使用したテンプレートでの作業に、ポータルのテンプレート エディターは使用できません。 このスタイルのコメントを追加する場合は、ご使用のツールでインライン JSON コメントがサポートされていることを確認してください。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
      "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
      "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

VS Code では、コメントを使用した JSON に言語モードを設定できます。 インライン コメントは無効としてマークされなくなります。 モードを変更するには:

1. 言語モードの選択を開きます (Ctrl + K M)

1. **[JSON with Comments]\(コメントを使用した JSON\)** を選択します。

   ![言語モードの選択](./media/resource-group-authoring-templates/select-json-comments.png)

## <a name="template-limits"></a>テンプレートの制限

テンプレートのサイズを 1 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 1 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 

また、以下のように制限されます。

* パラメーター 256 個
* 変数 256 個
* リソース (コピー数を含む) 800 個
* 出力値 64 個
* テンプレート式内で 24,576 文字

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](resource-manager-objects-as-parameters.md)に関する記事をご覧ください。

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* テンプレート作成に関する推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* すべての Azure 環境および Azure Stack で使用できる Resource Manager テンプレートの作成に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」をご覧ください。
