---
title: Bicep の関数 - scopes
description: デプロイのスコープに関する値を取得するために、Bicep ファイル内で使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 4432aa528dbdd565128a15eb79d4b4c01572c924
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827441"
---
# <a name="scope-functions-for-bicep"></a>Bicep の Scope 関数

この記事では、スコープの値を取得するための Bicep 関数について説明します。

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

スコープを管理グループに設定するために使用されるオブジェクトを返します。

### <a name="remarks"></a>解説

`managementGroup()` は、[管理グループのデプロイ](deploy-to-management-group.md)上でのみ使用できます。 これは、デプロイ操作の現在の管理グループを返します。

`managementGroup(name)` は、任意のデプロイ スコープに使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| name |いいえ |string |デプロイ先の管理グループの一意識別子。 管理グループの表示名は使用しないでください。 値を指定しない場合は、現在の管理グループが返されます。 |

### <a name="return-value"></a>戻り値

[モジュール](modules.md#configure-module-scopes)または[拡張リソースの種類](scope-extension-resources.md)上で `scope` プロパティを設定するために使用されるオブジェクト。

### <a name="management-group-example"></a>管理グループの例

次の例では、モジュールのスコープを管理グループに設定します。

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
``` 

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

スコープをリソース グループに設定するために使用されるオブジェクトを返します。

または

現在のリソース グループを表すオブジェクトを返します。

### <a name="remarks"></a>解説

resourceGroup 関数には、2 つの異なる用途があります。 1 つは、[モジュール](modules.md#configure-module-scopes)または[拡張リソースの種類](scope-extension-resources.md)上でスコープを設定すること、 もう 1 つは、現在のリソース グループに関する詳細を取得することです。 その用途は、関数の配置によって決まります。 `scope` プロパティの設定に使用すると、スコープ オブジェクトが返されます。

`resourceGroup()` は、スコープの設定またはリソース グループに関する詳細の取得に使用できます。

`resourceGroup(resourceGroupName)` と `resourceGroup(subscriptionId, resourceGroupName)` は、スコープの設定にのみ使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
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

### <a name="remarks"></a>解説

subscription 関数には、2 つの異なる用途があります。 1 つは、[モジュール](modules.md#configure-module-scopes)または[拡張リソースの種類](scope-extension-resources.md)上でスコープを設定すること、 もう 1 つは、現在のサブスクリプションに関する詳細を取得することです。 その用途は、関数の配置によって決まります。 `scope` プロパティの設定に使用すると、スコープ オブジェクトが返されます。

`subscription(subscriptionId)` は、スコープの設定にのみ使用できます。

`subscription()` は、スコープの設定またはサブスクリプションに関する詳細の取得に使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
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

### <a name="remarks"></a>解説

`tenant()` は、任意のデプロイ スコープに使用できます。 常に、現在のテナントが返されます。

### <a name="return-value"></a>戻り値

[モジュール](modules.md#configure-module-scopes)または[拡張リソースの種類](scope-extension-resources.md)上で `scope` プロパティを設定するために使用されるオブジェクト。

### <a name="tenant-example"></a>テナントの例

次の例は、テナントにデプロイするモジュールを表示します。

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>次のステップ

デプロイのスコープの詳細については、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループへのデプロイ](deploy-to-management-group.md)
* [テナントへのデプロイ](deploy-to-tenant.md)
