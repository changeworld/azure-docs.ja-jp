---
title: テンプレート関数 - デプロイ
description: Azure Resource Manager テンプレート (ARM テンプレート) で、デプロイ情報を取得するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 12f449cd70f0ccbeeb0d232299f38fb814a8cb24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733336"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM テンプレートのデプロイ関数

Resource Manager には、Azure Resource Manager テンプレート (ARM テンプレート) の現在のデプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](template-functions-resource.md)」を参照してください。

## <a name="deployment"></a>deployment

`deployment()`

現在のデプロイ操作に関する情報を返します。

### <a name="return-value"></a>戻り値

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されたオブジェクトのプロパティは、以下に該当するかどうかによって異なります。

* テンプレートまたはテンプレート スペックをデプロイする。
* ローカル ファイルであるテンプレートをデプロイするか、URI を使用してアクセスされるリモート ファイルであるテンプレートをデプロイする。
* リソース グループにデプロイするか、他のいずれかのスコープ ([Azure サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、または[テナント](deploy-to-tenant.md)) にデプロイする。

ローカル テンプレートをリソース グループにデプロイする場合は、関数から次の形式が返されます。

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

リモート テンプレートをリソース グループにデプロイする場合は、関数から次の形式が返されます。

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
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

テンプレート スペックをリソース グループにデプロイする場合は、関数から次の形式が返されます。

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Azure サブスクリプション、管理グループ、またはテナントにデプロイする場合は、返されるオブジェクトに `location` プロパティが含まれています。 ローカル テンプレートまたは外部テンプレートのいずれかをデプロイする場合は、場所プロパティが含まれています。 形式は次のようになります:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>解説

`deployment()` を使い、親テンプレートの URI に基づいて、別のテンプレートにリンクできます。

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

ポータル内のデプロイ履歴からテンプレートを再デプロイすると、テンプレートがローカル ファイルとしてデプロイされます。 デプロイ関数の中では、`templateLink` プロパティは返されません。 お使いのテンプレートが `templateLink` に依存して別のテンプレートへのリンクを構成している場合は、ポータルを使用して再デプロイしないでください。 代わりに、最初にテンプレートのデプロイに利用したコマンドを使用してください。

### <a name="example"></a>例

次の例では、デプロイ オブジェクトが返されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deployment.json":::

前の例では、次のオブジェクトが返されます。

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

サブスクリプションのデプロイの場合、次の例ではデプロイ オブジェクトが返されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deploymentsubscription.json":::

## <a name="environment"></a>環境

`environment()`

デプロイに使用される Azure 環境に関する情報を返します。

### <a name="return-value"></a>戻り値

この関数では、現在の Azure 環境のプロパティが返されます。 次の例では、グローバル Azure のプロパティを示しています。 ソブリン クラウドでは、少し異なるプロパティが返される場合があります。

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>例

次のテンプレート例では、環境オブジェクトが返されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/environment.json":::

前の例では、グローバル Azure にデプロイされる場合は、次のオブジェクトが返されます。

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

Bicep では、シンボリック名を使用してパラメーターを直接参照します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| parameterName |はい |string |返されるパラメーターの名前。 |

### <a name="return-value"></a>戻り値

指定したパラメーターの値。

### <a name="remarks"></a>注釈

通常、パラメーターを使ってリソースの値を設定します。 次の例では、Web サイトの名前を、デプロイ時に渡されるパラメーターの値に設定します。

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

### <a name="example"></a>例

次の例では、parameters 関数の簡単な使用方法を示しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/parameters.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

パラメーターの使用方法の詳細については、「[ARM テンプレートのパラメーター](./parameters.md)」を参照してください。

## <a name="variables"></a>variables

`variables(variableName)`

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

Bicep では、シンボリック名を使用して変数を直接参照します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| variableName |はい |String |返す変数の名前。 |

### <a name="return-value"></a>戻り値

指定した変数の値。

### <a name="remarks"></a>注釈

通常、変数を使って、複雑な値を 1 回だけ作成することによりテンプレートを簡略化します。 次の例では、ストレージ アカウントの一意の名前を作成します。

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

### <a name="example"></a>例

次の例は異なる変数値を返します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/variables.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

変数の使用方法の詳細については、「[ARM テンプレートでの変数](./variables.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。
