---
title: 拡張リソースの種類のスコープ (Bicep)
description: Bicep を使用して拡張リソースの種類を展開するときにスコープ プロパティを使用する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59b6576dfb1bd5e0ac4f56e6a59b6ea4d5c4b5f4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027248"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>Bicep で拡張リソースのスコープを設定する

拡張リソースは、別のリソースを変更するリソースです。 たとえば、リソースにロールを割り当てることができます。 ロールの割り当ては、拡張リソースの種類の 1 つです。

拡張リソースの種類の完全な一覧については、「[他のリソースの機能を拡張するリソースの種類](../management/extension-resource-types.md)」を参照してください。

この記事では、Bicep ファイルを使用して展開されている場合に、拡張リソースの種類のスコープを設定する方法について説明します。 リソースに適用するときに拡張リソースで使用できる scope プロパティについて説明します。

> [!NOTE]
> スコープ プロパティは、拡張リソースの種類でのみ使用できます。 拡張の種類ではないリソースの種類に別のスコープを指定するには、[モジュール](modules.md)を使用します。

## <a name="apply-at-deployment-scope"></a>展開スコープで適用する

ターゲット展開スコープで拡張リソースの種類を適用するには、他のリソースの種類と同様に、リソースをテンプレートに追加します。 利用可能なスコープは、[リソース グループ](deploy-to-resource-group.md)、[サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、[テナント](deploy-to-tenant.md)です。 展開スコープで、そのリソースの種類がサポートされている必要があります。

次のテンプレートによって、ロックが展開されます。

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

次の例では、ロールを割り当てます。

```bicep
targetScope = 'subscription'

@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}

resource roleAssignSub 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
}
```

## <a name="apply-to-resource"></a>リソースに適用

リソースに拡張リソースを適用するには、`scope` プロパティを使用します。 scope プロパティを、拡張を追加するリソースの名前に設定します。 scope プロパティは、拡張リソースの種類のルート プロパティです。

次の例では、ストレージ アカウントを作成し、それにロールを適用します。

```bicep
@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()
param location string = resourceGroup().location

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}
var uniqueStorageName = 'storage${uniqueString(resourceGroup().id)}'

resource storageName 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource roleAssignStorage 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
  scope: storageName
  dependsOn: [
    storageName
  ]
}
```

## <a name="next-steps"></a>次のステップ

スコープへのデプロイの詳細については、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループのデプロイ](deploy-to-management-group.md)
* [テナントのデプロイ](deploy-to-tenant.md)