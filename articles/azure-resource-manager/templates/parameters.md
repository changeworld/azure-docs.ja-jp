---
title: テンプレート内のパラメーター
description: Azure Resource Manager テンプレート (ARM テンプレート) でパラメーターを定義する方法について説明します。
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 5c94dc3f4d37fa6c08e29e03e88dd3ba54e68271
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625527"
---
# <a name="parameters-in-arm-templates"></a>ARM テンプレートのパラメーター

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境でテンプレートを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 テンプレートでパラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

各パラメーターは、いずれかの[データ型](data-types.md)に設定する必要があります。

## <a name="minimal-declaration"></a>最小限の宣言

少なくとも、すべてのパラメーターには名前と型が必要です。

テンプレートを Azure portal 経由でデプロイすると、キャメル ケースのパラメーター名はスペース区切りの名前に変換されます。 たとえば、次の例の *demoString* は *Demo String* と表示されます。 詳細については、「[デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](./deploy-to-azure-button.md)」と「[ARM テンプレートと Azure portal でリソースをデプロイする](./deploy-portal.md)」をご覧ください。

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

## <a name="secure-parameters"></a>セキュリティで保護されたパラメーター

文字列またはオブジェクトのパラメーターをセキュリティで保護されたものとマークすることができます。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

## <a name="allowed-values"></a>使用できる値

パラメーターに使用できる値を定義できます。 使用できる値は配列で指定します。 使用できる値の 1 つではない値がパラメーターに渡された場合、検証時にデプロイは失敗します。

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

## <a name="default-value"></a>既定値

パラメーターの既定値を指定できます。 既定値は、デプロイ時に値が指定されない場合に使用されます。

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

パラメーターの他のプロパティと共に既定値を指定するには、次の構文を使用します。

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

既定値を指定した式を使用できます。 パラメーター セクションでは、[reference](template-functions-resource.md#reference) 関数も、いずれの [list](template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

他のパラメーター プロパティと共に式を使用することはできません。

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

別のパラメーター値を使用して既定値を作成できます。 次のテンプレートを使用すると、サイト名からホスト プラン名が作成されます。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

## <a name="length-constraints"></a>長さの制限

文字列と配列のパラメーターの最小長と最大長を指定できます。 一方または両方の制約を設定できます。 文字列の場合、長さは文字数を示します。 配列の場合、長さは配列内の項目数を示します。

次の例では、2 つのパラメーターを宣言しています。 1 つ目のパラメーターは、文字数が 3-24 である必要があるストレージ アカウント名用です。 もう 1 つのパラメーターは、項目数が 1-5 個である必要がある配列です。

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

## <a name="integer-constraints"></a>整数の制約

整数パラメーターの最小値と最大値を設定できます。 一方または両方の制約を設定できます。

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

## <a name="description"></a>説明

テンプレートのユーザーが指定する値を理解できるように、パラメーターに説明を追加することができます。 ポータルからテンプレートをデプロイする場合、説明で指定したテキストがそのパラメーターのヒントとして自動的に使用されます。 パラメーター名から推測できる情報よりもテキストからわかる情報の方が多い場合にのみ、説明を追加します。

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

## <a name="use-parameter"></a>パラメーターを使用する

パラメーターの値を参照するには、[parameters](template-functions-deployment.md#parameters) 関数を使用します。 次の例では、キー コンテナー名にパラメーター値が使用されています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2021-06-01-preview",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理できます。 この方法により、テンプレート内のパラメータ数も減少します。

次の例は、オブジェクトであるパラメーターを示しています。 既定値は、オブジェクトの予想されるプロパティを示しています。 これらのプロパティは、デプロイするリソースを定義するときに使用されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2021-02-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、パラメーターを使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |

## <a name="next-steps"></a>次のステップ

* パラメーターに使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](./syntax.md)」をご覧ください。
* パラメーター値をファイルとして渡す方法については、「[Resource Manager パラメーター ファイルの作成](parameter-files.md)」を参照してください。
* パラメーターの作成に関する推奨事項については、[ベスト プラクティス - パラメーター](./best-practices.md#parameters)に関する記事をご覧ください。
