---
title: 拡張リソースの種類のスコープ (Bicep)
description: Bicep を使用して拡張リソースの種類を展開するときにスコープ プロパティを使用する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8d91aa1109db4b1d884e90e3e0744611f9dbf4d8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549852"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>Bicep で拡張リソースのスコープを設定する

拡張リソースは、別のリソースを変更するリソースです。 たとえば、リソースにロールを割り当てることができます。 ロールの割り当ては、拡張リソースの種類の 1 つです。

拡張リソースの種類の完全な一覧については、「[他のリソースの機能を拡張するリソースの種類](../management/extension-resource-types.md)」を参照してください。

この記事では、Bicep ファイルを使用して展開されている場合に、拡張リソースの種類のスコープを設定する方法について説明します。 リソースに適用するときに拡張リソースで使用できる scope プロパティについて説明します。

> [!NOTE]
> スコープ プロパティは、拡張リソースの種類でのみ使用できます。 拡張の種類ではないリソースの種類に別のスコープを指定するには、[モジュール](modules.md)を使用します。

### <a name="microsoft-learn"></a>Microsoft Learn

拡張リソースの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[Bicep を使用して子と拡張機能のリソースをデプロイする](/learn/modules/child-extension-bicep-templates)」を参照してください。

## <a name="apply-at-deployment-scope"></a>展開スコープで適用する

ターゲット展開スコープで拡張リソースの種類を適用するには、他のリソースの種類と同様に、リソースをテンプレートに追加します。 利用可能なスコープは、[リソース グループ](deploy-to-resource-group.md)、[サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、[テナント](deploy-to-tenant.md)です。 展開スコープで、そのリソースの種類がサポートされている必要があります。

リソース グループにデプロイすると、次のテンプレートによってそのリソース グループにロックが追加されます。

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

次の例では、デプロイ先のサブスクリプションにロールが割り当てられます。

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

リソースに拡張リソースを適用するには、`scope` プロパティを使用します。 scope プロパティで、拡張を追加するリソースを参照します。 リソースを参照するには、リソースのシンボリック名を指定します。 scope プロパティは、拡張リソースの種類のルート プロパティです。

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

resource demoStorageAcct 'Microsoft.Storage/storageAccounts@2019-04-01' = {
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
  scope: demoStorageAcct
}
```

既存のリソースに拡張機能リソースを適用できます。 次の例では、既存のストレージ アカウントにロックが追加されます。

```bicep
resource demoStorageAcct 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: 'examplestore'
}

resource createStorageLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'storeLock'
  scope: demoStorageAcct
  properties: {
    level: 'CanNotDelete'
    notes: 'Storage account should not be deleted.'
  }
}
```

デプロイのターゲット スコープとは異なるスコープを対象とする場合、他のリソースと同じ要件が拡張機能リソースに適用されます。 複数のスコープへのデプロイについては、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループへのデプロイ](deploy-to-management-group.md)
* [テナントへのデプロイ](deploy-to-tenant.md)

## <a name="next-steps"></a>次のステップ

拡張リソースの種類の完全な一覧については、「[他のリソースの機能を拡張するリソースの種類](../management/extension-resource-types.md)」を参照してください。
