---
title: Bicep を使用した条件付きデプロイ
description: リソースを Bicep を使用して条件付きでデプロイする方法を説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 9636444af81b000443dc72cf6e3fc1d8d6da5093
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537085"
---
# <a name="conditional-deployment-in-bicep"></a>Bicep における条件付きデプロイ

オプションで Bicep 内のリソースをデプロイする必要があることがあります。 `if` キーワードを使用してリソースをデプロイするかどうかを指定します。 この条件の値は true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

> [!NOTE]
> 条件付きのデプロイは[子リソース](child-resource-name-type.md)にはカスケードされません。 リソースとその子リソースを条件付きでデプロイする場合は、リソースの種類ごとに同じ条件を適用する必要があります。

## <a name="deploy-condition"></a>デプロイの条件

リソースをデプロイするかどうかを示すパラメーター値を渡すことができます。 次の例では、DNS ゾーンを条件付きでデプロイします。

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

条件は、依存関係の宣言と共に使用できます。 条件付きリソースの識別子が別のリソース (明示的な依存関係) の `dependsOn` で指定されている場合、テンプレートのデプロイ時に条件が false と評価されると依存関係は無視されます。 条件の評価結果が true であると依存関係は尊重されます。 条件付きリソース (暗黙の依存関係) のプロパティを参照することはできますが、場合によっては実行時エラーが発生することがあります。

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

`condition` 要素を使用する完全なテンプレート例については、[新規または既存の仮想ネットワーク、ストレージ、およびパブリック IP を使用する VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions) に関するページを参照してください。

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

他のリソースと同じように、条件付きリソースにる[依存するリソース](./resource-declaration.md#set-resource-dependencies)として設定します。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

## <a name="next-steps"></a>次の手順

* 条件とループに関する Microsoft Learn モジュールについては、「[条件とループを使用した柔軟な Bicep テンプレートの作成](/learn/modules/build-flexible-bicep-templates-conditions-loops/)」を参照してください。
* Bicep ファイルの作成に関する推奨事項については、「[Bicep のベストプラクティス](best-practices.md)」を参照してください。
* リソースから複数のインスタンスを作成するには、「[Bicep でのリソースのイテレーション](loop-resources.md)」を参照してください。
