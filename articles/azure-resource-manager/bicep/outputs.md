---
title: Bicep の出力
description: Bicep の出力値を定義する方法について説明します。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 95366013ed972e0251c99732ea4b344b5b94e743
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027224"
---
# <a name="outputs-in-bicep"></a>Bicep の出力

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) および Bicep ファイルで出力値を定義する方法について説明します。 デプロイされたリソースから値を返す必要がある場合に出力を使用します。

各出力値の形式は、いずれかの[データ型](data-types.md)に解決される必要があります。

## <a name="define-output-values"></a>出力値の定義

次の例は、`output` キーワードを使用して、デプロイされたリソースからプロパティを返す方法を示しています。

次の例では、`publicIP` は Bicep ファイルにデプロイされているパブリック IP アドレスの識別子 (シンボリック名) です。 出力値には、パブリック IP アドレスの完全修飾ドメイン名が取得されます。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

名前にハイフンが含まれているプロパティを出力する必要がある場合は、ドット表記ではなく、名前を角かっこで囲みます。 たとえば、`.property-name` の代わりに `['property-name']` を使用します。

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>条件付き出力

値を条件付きで返すことができます。 通常、リソースを[条件付きでデプロイ](conditional-resource-deployment.md)した場合に条件付き出力を使用します。 次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

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

場合により、テンプレートの作成時に、返す必要がある値のインスタンスの数が不明なシナリオもあります。 反復出力を使用すると、可変数の値を返すことができます。

Bicep で、動的出力の条件を定義する `for` 式を追加します。 次の例では、配列を反復処理します。

```bicep
param nsgLocation string = resourceGroup().location
param nsgNames array = [
  'nsg1'
  'nsg2'
  'nsg3'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in nsgNames: {
  name: name
  location: nsgLocation
}]

output nsgs array = [for (name, i) in nsgNames: {
  name: nsg[i].name
  resourceId: nsg[i].id
}]
```

また、整数の範囲を反復処理することもできます。 詳細については、「[Bicep での出力の反復処理](loop-outputs.md)」を参照してください。

## <a name="modules"></a>モジュール

モジュールを使用して関連するテンプレートをデプロイできます。 モジュールから出力値を取得するには、次の構文を使用します。

```bicep
<module-name>.outputs.<property-name>
```

次の例では、モジュールから値を取得して、ロード バランサーの IP アドレスを設定する方法を示します。 モジュールの名前は `publicIP` です。

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>テンプレートの例

次のテンプレートでは、リソースはデプロイされません。 さまざまな型の出力を返すいくつかの方法を示します。

Bicep では現在、ループはサポートされていません。

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

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
