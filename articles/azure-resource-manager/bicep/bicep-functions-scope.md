---
title: Bicep の関数 - scopes
description: デプロイのスコープに関する値を取得するために、Bicep ファイル内で使用する関数について説明します。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 809d3b2097e530f92370374be5f15c39bbd70734
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178343"
---
# <a name="scope-functions-for-bicep"></a>Bicep の Scope 関数

この記事では、スコープの値を取得するための Bicep 関数について説明します。

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(identifier)`

スコープを管理グループに設定するために使用されるオブジェクトを返します。

または

現在のデプロイの管理グループからプロパティを持つオブジェクトを返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>解説

`managementGroup()` は、[管理グループのデプロイ](deploy-to-management-group.md)上でのみ使用できます。 これは、デプロイ操作の現在の管理グループを返します。 スコープ オブジェクトを取得するとき、または現在の管理グループのプロパティを取得するときに使用します。

`managementGroup(identifier)` は、任意のデプロイ スコープに使用できますが、スコープ オブジェクトを取得する場合のみになります。 管理グループのプロパティを取得するために、管理グループの識別子を渡すことはできません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| identifier |いいえ |string |デプロイ先の管理グループの一意識別子。 管理グループの表示名は使用しないでください。 値を指定しない場合は、現在の管理グループが返されます。 |

### <a name="return-value"></a>戻り値

[モジュール](modules.md#set-module-scope)または[拡張リソースの種類](scope-extension-resources.md)上で `scope` プロパティを設定するために使用されるオブジェクト。 または、現在の管理グループのプロパティを持つオブジェクト。

### <a name="management-group-example"></a>管理グループの例

次の例では、モジュールのスコープを管理グループに設定します。

```bicep
param managementGroupIdentifier string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupIdentifier)
}
```

次の例では、現在の管理グループのプロパティが返されます。

```bicep
targetScope = 'managementGroup'

var mgInfo = managementGroup()

output mgResult object = mgInfo
```

戻り値は次のとおりです。

```json
"mgResult": {
  "type": "Object",
  "value": {
    "id": "/providers/Microsoft.Management/managementGroups/examplemg1",
    "name": "examplemg1",
    "properties": {
      "details": {
        "parent": {
          "displayName": "Tenant Root Group",
          "id": "/providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000",
          "name": "00000000-0000-0000-0000-000000000000"
        },
        "updatedBy": "00000000-0000-0000-0000-000000000000",
        "updatedTime": "2020-07-23T21:05:52.661306Z",
        "version": "1"
      },
      "displayName": "Example MG 1",
      "tenantId": "00000000-0000-0000-0000-000000000000"
    },
    "type": "/providers/Microsoft.Management/managementGroups"
  }
}
```

次の例では、新しい管理グループを作成し、この関数を使用して親管理グループを設定します。

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

スコープをリソース グループに設定するために使用されるオブジェクトを返します。

または

現在のリソース グループを表すオブジェクトを返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>解説

resourceGroup 関数には、2 つの異なる用途があります。 1 つは、[モジュール](modules.md#set-module-scope)または[拡張リソースの種類](scope-extension-resources.md)上でスコープを設定すること、 もう 1 つは、現在のリソース グループに関する詳細を取得することです。 その用途は、関数の配置によって決まります。 `scope` プロパティの設定に使用すると、スコープ オブジェクトが返されます。

`resourceGroup()` は、スコープの設定またはリソース グループに関する詳細の取得に使用できます。

`resourceGroup(resourceGroupName)` と `resourceGroup(subscriptionId, resourceGroupName)` は、スコープの設定にのみ使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceGroupName |いいえ |string | デプロイ先のリソース グループの名前。 値を指定しない場合は、現在のリソース グループが返されます。 |
| subscriptionId |いいえ |string |デプロイ先のサブスクリプションの一意識別子。 値を指定しない場合は、現在のサブスクリプションが返されます。 |

### <a name="return-value"></a>戻り値

スコープの設定に使用すると、関数によって、モジュールまたは拡張リソースの種類上の `scope` プロパティに対して有効なオブジェクトが返されます。

リソース グループの詳細の取得に使用すると、関数によって次の形式が返されます。

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**managedBy** プロパティは、別のサービスによって管理されているリソースを含むリソース グループに対してのみ返されます。 Managed Applications、Databricks、および AKS の場合、プロパティの値は管理しているリソースのリソース ID になります。

### <a name="resource-group-example"></a>リソース グループの例

次の例は、モジュールのスコープをリソース グループに設定します。

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

次の例は、リソース グループのプロパティを返します。

```bicep
output resourceGroupOutput object = resourceGroup()
```

次の形式のオブジェクトが返されます。

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

resourceGroup 関数の一般的な用途では、リソース グループと同じ場所にリソースを作成します。 次の例では、既定のパラメーター値にリソース グループの場所を使用します。

```bicep
param location string = resourceGroup().location
```

resourceGroup 関数を使用して、リソース グループからリソースにタグを適用することもできます。 詳細については、「[リソース グループからタグを適用する](../management/tag-resources.md#apply-tags-from-resource-group)」を参照してください。

## <a name="subscription"></a>subscription

`subscription()`

`subscription(subscriptionId)`

スコープをサブスクリプションに設定するために使用されるオブジェクトを返します。

または

現在のデプロイのサブスクリプションの詳細を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>解説

subscription 関数には、2 つの異なる用途があります。 1 つは、[モジュール](modules.md#set-module-scope)または[拡張リソースの種類](scope-extension-resources.md)上でスコープを設定すること、 もう 1 つは、現在のサブスクリプションに関する詳細を取得することです。 その用途は、関数の配置によって決まります。 `scope` プロパティの設定に使用すると、スコープ オブジェクトが返されます。

`subscription(subscriptionId)` は、スコープの設定にのみ使用できます。

`subscription()` は、スコープの設定またはサブスクリプションに関する詳細の取得に使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |いいえ |string |デプロイ先のサブスクリプションの一意識別子。 値を指定しない場合は、現在のサブスクリプションが返されます。 |

### <a name="return-value"></a>戻り値

スコープの設定に使用すると、関数によって、モジュールまたは拡張リソースの種類上の `scope` プロパティに対して有効なオブジェクトが返されます。

サブスクリプションの詳細の取得に使用すると、関数によって次の形式が返されます。

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>サブスクリプションの例

次の例は、モジュールのスコープをサブスクリプションに設定します。

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

次の例は、サブスクリプションの詳細を返します。

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

スコープをテナントに設定するために使用されるオブジェクトを返します。

または

現在のデプロイのテナントに関するプロパティを返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>解説

`tenant()` は、任意のデプロイ スコープに使用できます。 常に、現在のテナントが返されます。 この関数を使用して、リソースのスコープを設定したり、現在のテナントのプロパティを取得したりできます。

### <a name="return-value"></a>戻り値

[モジュール](modules.md#set-module-scope)または[拡張リソースの種類](scope-extension-resources.md)上で `scope` プロパティを設定するために使用されるオブジェクト。 または、現在のテナントに関するプロパティを持つオブジェクト。

### <a name="tenant-example"></a>テナントの例

次の例は、テナントにデプロイするモジュールを表示します。

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

次の例では、テナントのプロパティが返されます。

```bicep
var tenantInfo = tenant()

output tenantResult object = tenantInfo
```

戻り値は次のとおりです。

```json
"tenantResult": {
  "type": "Object",
  "value": {
    "countryCode": "US",
    "displayName": "Contoso",
    "id": "/tenants/00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000"
  }
}
```

一部のリソースでは、プロパティのテナント ID を設定する必要があります。 テナント ID をパラメーターとして渡すのではなく、テナント関数を使用して取得できます。

```bicep
resource kv 'Microsoft.KeyVault/vaults@2021-06-01-preview' = {
  name: 'examplekeyvault'
  location: 'westus'
  properties: {
    tenantId: tenant().tenantId
    ...
  }
}
```

## <a name="next-steps"></a>次のステップ

デプロイのスコープの詳細については、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループへのデプロイ](deploy-to-management-group.md)
* [テナントへのデプロイ](deploy-to-tenant.md)
