---
title: Bicep の出力
description: Bicep の出力値を定義する方法について説明します。
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4ed640cd639b7a1b69cd07ae8ac50a4ddff436fb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491319"
---
# <a name="outputs-in-bicep"></a>Bicep の出力

この記事では、Bicep ファイルで出力値を定義する方法について説明します。 デプロイされたリソースから値を返す必要がある場合に出力を使用します。

## <a name="define-output-values"></a>出力値の定義

出力値を定義するための構文は次のとおりです。

```bicep
output <name> <data-type> = <value>
```

出力の名前を、パラメーター、変数、モジュール、またはリソースと同じにすることはできません。 各出力値は、いずれかの[データ型](data-types.md)に解決される必要があります。

次の例は、デプロイされたリソースからプロパティを返す方法を示しています。 この例では、`publicIP` は Bicep ファイルにデプロイされているパブリック IP アドレスのシンボリック名です。 出力値には、パブリック IP アドレスの完全修飾ドメイン名が取得されます。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

次の例は、さまざまな型の出力を返す方法を示しています。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

名前にハイフンが含まれているプロパティを出力する必要がある場合は、ドット表記ではなく、名前を角かっこで囲みます。 たとえば、`.property-name` の代わりに `['property-name']` を使用します。

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>条件付き出力

返す値がデプロイの状態によって異なる場合は、`?` 演算子を使用します。

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

通常、リソースを[条件付きでデプロイ](conditional-resource-deployment.md)した場合に条件付き出力を使用します。 次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

Bicep で条件付き出力を指定するには、`?` 演算子を使用します。 次の例では、条件に応じて、エンドポイントの URL または空の文字列を返します。

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>動的な出力の数

場合により、テンプレートの作成時に、返す必要がある値のインスタンスの数が不明なシナリオもあります。 `for` 要素を使用することで、可変数の値を返すことができます。

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

次の例では、配列を反復処理します。

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

詳しくは、「[Bicep の反復ループ](loops.md)」をご覧ください。

## <a name="outputs-from-modules"></a>モジュールからの出力

モジュールから出力値を取得するには、次の構文を使用します。

```bicep
<module-name>.outputs.<property-name>
```

次の例では、モジュールから値を取得して、ロード バランサーの IP アドレスを設定する方法を示します。 モジュールの名前は `publicIP` です。

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="get-output-values"></a>出力値の取得

デプロイが成功すると、出力値はデプロイの結果で自動的に返されます。

デプロイ履歴から出力値を取得するには、Azure CLI または Azure PowerShell スクリプトを使用できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>次のステップ

* 出力に使用できるプロパティの詳細については、「[Bicep の構造と構文について](./file.md)」を参照してください。
