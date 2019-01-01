---
title: Azure Resource Manager テンプレートの parameters セクション | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの parameters セクションについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomfitz
ms.openlocfilehash: 83ba1b94413990c0eb8dff42c49d46456a658d5a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417771"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの parameters セクション
テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 特定の環境 (開発、テスト、運用など) に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 テンプレートでは必ずしもパラメーターを使用する必要はありませんが、パラメーターを使わなかった場合、常に同じリソースが同じ名前、同じ場所、同じプロパティでデプロイされます。

テンプレートではパラメーターが 256 個に制限されます。 この記事で説明するように、複数のプロパティを含むオブジェクトを使用すると、パラメーター数を減らすことができます。

## <a name="define-and-use-a-parameter"></a>パラメーターの定義と使用

単純なパラメーター定義の例を次に示します。 ここでは、パラメーターの名前が定義され、パラメーターが文字列値を取ることが指定されています。 パラメーターは、使用目的に合う値のみを受け入れます。 デプロイ時に値が指定されなかった場合の既定値が指定されています。 さらに、このパラメーターには使用方法の説明も含まれています。 

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

テンプレートでは、次の構文でパラメーターの値を参照できます。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>利用可能なプロパティ

前の例では、parameters セクションで使用できるプロパティの一部のみを示しました。 利用可能なプロパティは次のとおりです。

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

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| parameterName |はい |パラメーターの名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 |
| defaultValue |いいえ  |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ  |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ  |int 型パラメーターの最小値。 |
| maxValue |いいえ  |int 型パラメーターの最大値。 |
| minLength |いいえ  |文字列型、securestring 型、配列型パラメーターの長さの最小値。 |
| maxLength |いいえ  |文字列型、securestring 型、配列型パラメーターの長さの最大値。 |
| description |いいえ  |ポータルを通じてユーザーに表示されるパラメーターの説明。 |

## <a name="template-functions-with-parameters"></a>テンプレート関数とパラメーター

パラメーターの既定値を指定する場合は、ほとんどのテンプレート関数を使用できます。 別のパラメーター値を使用して既定値を作成できます。 次のテンプレートは、既定値に関する関数の使用方法を示します。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

parameters セクションで `reference` 関数を使用することはできません。 パラメーターはデプロイ前に評価されるため、`reference` 関数はリソースのランタイム状態を取得できません。 

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

テンプレートでパラメーターを定義し、デプロイ時に 1 つの値ではなく、JSON オブジェクトを指定します。 

```json
"parameters": {
  "VNetSettings": {
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
```

その後、ドット演算子を使用して、パラメーターのサブプロパティを参照します。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Recommendations
パラメーターを使用する場合は、次の情報を活用してください。

* パラメーターの使用を最小限に抑えます。 可能であれば、変数またはリテラル値を使用します。 パラメーターは、次のようなシナリオに対してのみ使用します。
   
   * 環境 (SKU、サイズ、処理能力) に応じてさまざまな設定を使用する場合
   * 特定しやすいリソース名を付ける場合
   * 他のタスクを実行するために頻繁に使用する値 (管理者ユーザー名など) がある場合
   * シークレット情報 (パスワードなど)
   * リソースの種類のインスタンスを複数作成するときに多数の値または値の配列を使用する場合。
* パラメーター名にキャメル ケースを使用します。
* メタデータですべてのパラメーターを説明します。

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* パラメーターの既定値を定義してください (パスワードおよび SSH キーは除く)。 既定値を指定すると、パラメーターはデプロイ時に省略可能になります。 既定値として空の文字列を指定できます。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* すべてのパスワードおよびシークレット情報には **securestring** を使用してください。 JSON オブジェクトに機密データを渡す場合は、**secureObject** 型を使用します。 securestring 型または secureObject 型を含むテンプレート パラメーターをリソースのデプロイ後に読み取ることはできません。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* パラメーターを使って場所を指定し、同じ場所に配置される可能性があるリソースでできる限りそのパラメーターを共有します。 この方法により、ユーザーが場所情報の入力を求められる回数を最小限に抑えることができます。 リソースの種類がサポートされる場所に限りがある場合は、有効な場所をテンプレートで直接指定するか、または別の location パラメーターを追加することが必要になる場合があります。 組織でユーザーに許可されるリージョンが制限されている場合、**resourceGroup().location** 式が原因でユーザーがテンプレートをデプロイできなくなる可能性があります。 たとえば、あるユーザーがリージョンにリソース グループを作成します。 別のユーザーはそのリソース グループにデプロイする必要がありますが、リージョンにアクセスできません。 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* リソースの種類の API バージョンにはパラメーターや変数を使用しないでください。 リソースのプロパティおよび値は、バージョン番号ごとに異なる可能性があります。 パラメーターまたは変数に API バージョンが設定されると、コード エディターの IntelliSense が適切なスキーマを決定できなくなります。 代わりに、テンプレートの API バージョンをハードコーディングしてください。
* テンプレートでは、デプロイ コマンドのパラメーターと一致するパラメーター名は指定しないでください。 Resource Manager では、接尾辞 **FromTemplate** をテンプレート パラメーターに追加することで、このような名前の競合を防ぎます。 たとえば、**ResourceGroupName** という名前のパラメーターをテンプレートに追加した場合、このパラメーターは、[New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合するため、 デプロイ中、**ResourceGroupNameFromTemplate** に値を指定するように求められます。

## <a name="example-templates"></a>サンプル テンプレート

次のサンプル テンプレートでは、パラメーターを使用するいくつかのシナリオを例示します。 さまざまなシナリオでパラメーターがどのように処理されるかを、このサンプルをデプロイして試してください。

|テンプレート  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |

## <a name="next-steps"></a>次の手順

* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* デプロイ時にパラメーター値を入力する方法については、 [Azure Resource Manager テンプレートを使用したリソースのデプロイ](resource-group-template-deploy.md)に関するページをご覧ください。 
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* パラメーター オブジェクトの使用方法について詳しくは、「[Azure Resource Manager テンプレートのパラメーターとしてオブジェクトを使用する](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)」をご覧ください。
