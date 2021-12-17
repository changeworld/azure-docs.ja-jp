---
title: Bicep を使用した条件付きデプロイ
description: リソースを Bicep を使用して条件付きでデプロイする方法を説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 0c2f154a0c45dd46a56a4321aa67a75e2263b317
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219604"
---
# <a name="conditional-deployment-in-bicep"></a>Bicep における条件付きデプロイ

オプションで Bicep を使用してリソースまたはモジュールをデプロイする必要があることがあります。 `if` キーワードを使用して、リソースまたはモジュールをデプロイするかどうかを指定します。 この条件の値は true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この値は、リソースまたはモジュール全体にのみ適用できます。

> [!NOTE]
> 条件付きのデプロイは[子リソース](child-resource-name-type.md)にはカスケードされません。 リソースとその子リソースを条件付きでデプロイする場合は、リソースの種類ごとに同じ条件を適用する必要があります。

### <a name="microsoft-learn"></a>Microsoft Learn

条件の詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[条件とループを使用して柔軟な Bicep テンプレートを作成する](/learn/modules/build-flexible-bicep-templates-conditions-loops/)」を参照してください。

## <a name="deploy-condition"></a>デプロイの条件

リソースをデプロイするかどうかを示すパラメーター値を渡すことができます。 次の例では、DNS ゾーンを条件付きでデプロイします。

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

次の例では、条件付きでモジュールをデプロイします。

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

条件は、依存関係の宣言と共に使用できます。 [明示的な依存関係](resource-declaration.md#dependencies)の場合、リソースがデプロイされていないと、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。 暗黙的な依存関係の場合、条件付きリソースのプロパティを参照することはできますが、デプロイ エラーが発生する可能性があります。

## <a name="new-or-existing-resource"></a>新規または既存のリソース

条件付きデプロイを使用して、新しいリソースを作成したり、既存のリソースを使用したりすることができます。 次の例は、新しいストレージ アカウントをデプロイするか、または既存のストレージ アカウントを使用する方法を示しています。

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

パラメーター `newOrExisting` が **new** に設定されると、その条件は true に評価されます。 ストレージ アカウントはデプロイされます。 ただし、`newOrExisting` が **existing** に設定されると、その条件は false に評価され、ストレージ アカウントはデプロイされません。

## <a name="runtime-functions"></a>ランタイム関数

条件付きでデプロイされるリソースで [reference](./bicep-functions-resource.md#reference) または [list](./bicep-functions-resource.md#list) 関数を使用した場合、この関数はリソースがデプロイされていなくても評価されます。 この関数が存在しないリソースを参照する場合、エラーが返されます。

リソースがデプロイされるときにのみ条件に対してこの関数が評価されるようにするには、[条件式 ?](./operators-logical.md#conditional-expression--) 演算子を使用します。 次のテンプレート例では、条件付きで有効な式限定で、この関数を使用する方法を示しています。

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

## <a name="next-steps"></a>次の手順

* 条件とループに関する Microsoft Learn モジュールについては、「[条件とループを使用した柔軟な Bicep テンプレートの作成](/learn/modules/build-flexible-bicep-templates-conditions-loops/)」を参照してください。
* Bicep ファイルの作成に関する推奨事項については、「[Bicep のベストプラクティス](best-practices.md)」を参照してください。
* リソースから複数のインスタンスを作成するには、「[Bicep の反復ループ](loops.md)」を参照してください。
