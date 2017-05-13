---
title: "Azure Resource Manager テンプレートの関数 - デプロイ | Microsoft Docs"
description: "Azure Resource Manager テンプレートで、デプロイ情報を取得するために使用する関数について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: ce888415b6a5f82fb3d49834b055f8afe97442a8
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのデプロイ関数 

リソース マネージャーには、テンプレートのセクションから値を取得し、デプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](#deployment)
* [parameters](#parameters)
* [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](resource-group-template-functions-resource.md)」を参照してください。

<a id="deployment" />

## <a name="deployment"></a>deployment
`deployment()`

現在のデプロイ操作に関する情報を返します。

### <a name="examples"></a>例

次の例では、デプロイ オブジェクトが返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

次の例は、deployment() を使用し、親テンプレートの URI に基づいて別のテンプレートにリンクする方法を示しています。

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="return-value"></a>戻り値

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されるオブジェクトのプロパティは、デプロイ オブジェクトがリンクとして渡されたか、インライン オブジェクトとして渡されたかによって異なります。 デプロイ オブジェクトがインラインで渡された場合 (Azure PowerShell の **TemplateFile** パラメーターを使用してローカル ファイルを参照する場合など)、返されるオブジェクトは次の形式になります。

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

オブジェクトがリンクとして渡された場合 (**-TemplateUri** パラメーターを使用してリモート オブジェクトを参照する場合など)、オブジェクトは次の形式で返されます。 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```



<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| parameterName |あり |string |返されるパラメーターの名前。 |

### <a name="examples"></a>例

次の例では、parameters 関数の簡単な使用方法を示しています。

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="return-value"></a>戻り値

パラメーターの型。

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| variableName |はい |string |返す変数の名前。 |

### <a name="examples"></a>例

次の例では、変数の値を使用します。

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

### <a name="return-value"></a>戻り値

変数の型。

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


