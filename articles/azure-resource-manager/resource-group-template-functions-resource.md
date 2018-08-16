---
title: Azure Resource Manager テンプレートの関数 - リソース | Microsoft Docs
description: Azure Resource Manager テンプレートで、リソースに関する値を取得するために使用する関数について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: d3d2375b0b633beb56232e518202b09777f60cc8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524510"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのリソース関数

リソース マネージャーには、リソース値を取得する次の関数が用意されています。

* [listAccountSas](#list)
* [listKeys](#listkeys)
* [listSecrets](#list)
* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [サブスクリプション](#subscription)

パラメーター、変数、現在のデプロイから値を取得する方法については、「 [デプロイの値関数](resource-group-template-functions-deployment.md)」を参照してください。

<a id="listkeys" />
<a id="list" />

## <a name="listaccountsas-listkeys-listsecrets-and-list"></a>listAccountSas、listKeys、listSecrets、list*
`listAccountSas(resourceName or resourceIdentifier, apiVersion, functionValues)`

`listKeys(resourceName or resourceIdentifier, apiVersion)`

`listSecrets(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

list 操作をサポートする任意の種類のリソースの値を返します。 最も一般的に使用されるのは、`listKeys` と `listSecrets` です。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |[はい] |文字列 |リソースの一意識別子です。 |
| apiVersion |[はい] |文字列 |リソースのランタイム状態の API バージョン。 通常、**yyyy-mm-dd** の形式。 |
| functionValues |いいえ  |オブジェクト | 関数の値を持つオブジェクト。 このオブジェクトは、ストレージ アカウントの **listAccountSas** など、パラメーター値を持つオブジェクトの受信をサポートする関数に対してのみ指定します。 | 

### <a name="return-value"></a>戻り値

listKeys から返されるオブジェクトの形式は次のとおりです。

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

他のリスト関数の戻り値の形式はさまざまです。 関数の形式を確認するには、テンプレートの例に示すように、outputs セクションにその関数を含めてください。 

### <a name="remarks"></a>解説

**list** で始まるすべての操作は、テンプレート内で関数として使用できます。 使用可能な操作には、listKeys だけでなく、`list`、`listAdminKeys`、`listStatus` などの操作も含まれます。 [List Account SAS](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) 操作には、*signedExpiry* などの要求本文のパラメーターが必要です。 テンプレートでこの関数を使用するには、オブジェクトに本文のパラメーター値を指定します。

どのリソースの種類にリスト操作が含まれているのかを判断するには、次のオプションを使用できます。

* リソース プロバイダーの [REST API の操作](/rest/api/)に関するページを参照して、リスト操作を検索します。 たとえば、ストレージ アカウントには [listKeys 操作](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys)があります。
* [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) PowerShell コマンドレットを使用します。 次の例では、ストレージ アカウントのすべてのリスト操作が取得されます。

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 次の Azure CLI コマンドを使って、リスト操作のみをフィルター処理します。

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

リソース名または [resourceId 関数](#resourceid)を使用して、リソースを指定します。 参照されているリソースをデプロイするのと同じテンプレートでこの関数を使うときは、リソース名を使ってください。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json)は、outputs セクションでストレージ アカウントのプライマリ キーとセカンダリ キーを返す方法を示しています。 また、ストレージ アカウントの SAS トークンも返します。 そのトークンを取得するには、listAccountSas 関数にオブジェクトを渡します。 この例は、list 関数を使用する方法を示したものです。 通常、SAS トークンを出力値として返すのではなく、リソース値で使用します。 出力値はデプロイ履歴に格納され、セキュリティで保護されません。 デプロイを正常に行うには、将来の有効期限を指定する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "requestContent": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedResourceType": "c",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('requestContent'))]"
        }
    }
}
``` 

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storagename=<your-storage-account>
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storagename <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。 リソースの種類を指定しない場合、関数はリソース プロバイダーでサポートされているすべての種類を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| providerNamespace |[はい] |文字列 |プロバイダーの名前空間 |
| resourceType |いいえ  |文字列 |指定した名前空間内にあるリソースの種類。 |

### <a name="return-value"></a>戻り値

サポートされている各種類は、次の形式で返されます。 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

戻り値の配列の順序は保証されません。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)は、provider 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

**Microsoft.Web** リソース プロバイダーでリソースの種類が **sites** の場合、前の例では、次の形式のオブジェクトが返されます。

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

リソースのランタイム状態を表すオブジェクトを返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |[はい] |文字列 |名前またはリソースの一意の識別子。 |
| apiVersion |いいえ  |文字列 |指定したリソースの API バージョンです。 同じテンプレート内でリソースがプロビジョニングされない場合に、このパラメーターを追加します。 通常、**yyyy-mm-dd** の形式。 |
| 'Full' |いいえ  |文字列 |完全なリソース オブジェクトを返すかどうかを指定する値。 `'Full'` を指定しない場合、リソースのプロパティ オブジェクトのみが返されます。 完全なオブジェクトには、リソース ID や場所などの値が含まれます。 |

### <a name="return-value"></a>戻り値

あらゆるリソースの種類で、reference 関数のさまざまなプロパティが返されます。 この関数は、定義済みの単一の形式を返しません。 また、返される値は、完全なオブジェクトを指定したかどうかによって異なります。 あるリソースの種類のプロパティを確認するには、この例に示すように、outputs セクションでオブジェクトを返します。

### <a name="remarks"></a>解説

reference 関数はその値をランタイム状態から取得するので、変数セクションでは使用できません。 これは、テンプレートまたは[リンク済みテンプレート](resource-group-linked-templates.md#link-or-nest-a-template)の出力セクションで使用できます。 [入れ子になったテンプレート](resource-group-linked-templates.md#link-or-nest-a-template)の出力セクションで使用することはできません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。 

参照されているリソースを同じテンプレート内でプロビジョニングし、(リソース ID ではなく) 名前でリソースを参照する場合は、reference 関数を使用することにより、あるリソースが他のリソースに依存することを暗黙的に宣言します。 dependsOn プロパティを一緒に使用する必要はありません。 参照先のリソースがデプロイを完了するまで、関数は評価されません。

ある種類のリソースによって返されるプロパティの名前と値を確認するには、outputs セクションでオブジェクトを返すテンプレートを作成します。 その種類のリソースが既にある場合、このテンプレートは新しいリソースはデプロイせずにオブジェクトを返します。 

通常、**reference** 関数は、BLOB エンドポイント URI や完全修飾ドメイン名などのオブジェクトから特定の値を返すために使用します。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

`'Full'` は、プロパティのスキーマに含まれないリソースの値が必要なときに使用します。 たとえば、キー コンテナーのアクセス ポリシーを設定するために、仮想マシンの ID プロパティを取得する場合です。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

前のテンプレートの完全な例については、[Windows での Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json) に関するページを参照してください。 同様の例を [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json) についても利用できます。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)では、リソースをデプロイし、そのリソースを参照します。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

前の例では、2 つのオブジェクトが返されます。 properties オブジェクトの形式は次のとおりです。

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

完全なオブジェクトの形式は次のとおりです。

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json)では、このテンプレートでデプロイされていないストレージ アカウントが参照されます。 このストレージ アカウントは既に同じリソース グループ内に存在します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageAccountName=<your-storage-account>
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

現在のリソース グループを表すオブジェクトを返します。 

### <a name="return-value"></a>戻り値

返されるオブジェクトの形式は次のとおりです。

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>解説

resourceGroup 関数の一般的な用途では、リソース グループと同じ場所にリソースを作成します。 次の例では、リソース グループの場所を使用して、Web サイトの場所を割り当てます。

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)は、リソース グループのプロパティを返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

前の例では、次の形式のオブジェクトが返されます。

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

リソースの一意の識別子を返します。 リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |いいえ  |文字列 (GUID 形式) |既定値は、現在のサブスクリプションです。 別のサブスクリプション内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceGroupName |いいえ  |文字列 |既定値は、現在のリソース グループです。 別のリソース グループ内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceType |[はい] |文字列 |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |[はい] |文字列 |リソースの名前。 |
| resourceName2 |いいえ  |文字列 |リソースが入れ子になっている場合、次のリソース名セグメント。 |

### <a name="return-value"></a>戻り値

識別子は、次の形式で返されます。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>解説

指定するパラメーター値は、リソースが現在のデプロイと同じサブスクリプションおよびリソース グループに含まれるかどうかによって異なります。

同じサブスクリプションとリソース グループ内にあるストレージ アカウントのリソース ID を取得するには、次のようにします。

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

サブスクリプションは同じでもリソース グループが異なるストレージ アカウントのリソース ID を取得するには、次のようにします。

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

サブスクリプションとリソース グループが異なるストレージ アカウントのリソース ID を取得するには、次のようにします。

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

異なるリソース グループ内のデータベースのリソース ID を取得するには、次のようにします。

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。 次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)では、リソース グループ内にあるストレージ アカウントのリソース ID を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>サブスクリプション
`subscription()`

現在のデプロイのサブスクリプションの詳細を返します。 

### <a name="return-value"></a>戻り値

この関数は次の形式を返します。

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)は、outputs セクションで呼び出される subscription 関数を示しています。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

