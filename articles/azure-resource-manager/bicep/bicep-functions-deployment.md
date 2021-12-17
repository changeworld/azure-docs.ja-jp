---
title: Bicep の関数 - デプロイ
description: Bicep ファイルでデプロイ情報の取得に使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 9d06bd78d7ba6dcf0f73326f744e975a9c4e3f53
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352398"
---
# <a name="deployment-functions-for-bicep"></a>Bicep のデプロイ関数

この記事では、現在のデプロイに関連する値を取得するための Bicep 関数について説明します。

## <a name="deployment"></a>deployment

`deployment()`

現在のデプロイ操作に関する情報を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="return-value"></a>戻り値

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されたオブジェクトのプロパティは、以下に該当するかどうかによって異なります。

* ローカルの Bicep ファイルをデプロイする。
* リソース グループにデプロイするか、他のいずれかのスコープ ([Azure サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、または[テナント](deploy-to-tenant.md)) にデプロイする。

ローカルの Bicep ファイルをリソース グループにデプロイすると、関数から次の形式のデータが返されます。

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

Azure サブスクリプション、管理グループ、またはテナントにデプロイする場合は、返されるオブジェクトに `location` プロパティが含まれています。 ローカルの Bicep ファイルをデプロイするときは、場所のプロパティがそこに含まれます。 形式は次のようになります:

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

### <a name="example"></a>例

次の例では、デプロイ オブジェクトが返されます。

```bicep
output deploymentOutput object = deployment()
```

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

## <a name="environment"></a>環境

`environment()`

デプロイに使用される Azure 環境に関する情報を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

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

次の Bicep のファイルの例では、environment オブジェクトが返されます。

```bicep
output environmentOutput object = environment()
```

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

## <a name="next-steps"></a>次のステップ

* リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](./bicep-functions-resource.md)」を参照してください。
