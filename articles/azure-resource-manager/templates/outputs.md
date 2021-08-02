---
title: テンプレート内の出力
description: Azure Resource Manager テンプレート (ARM テンプレート) で出力値を定義する方法について説明します。
ms.topic: conceptual
ms.date: 05/18/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d81499ca7a85f8652ee353cde2e8e1cee48d4bf4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960348"
---
# <a name="outputs-in-arm-templates"></a>ARM テンプレート内の出力

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) で出力値を定義する方法について説明します。 デプロイされたリソースから値を返す必要がある場合に出力を使用します。

各出力値の形式は、いずれかの[データ型](data-types.md)に解決される必要があります。

## <a name="define-output-values"></a>出力値の定義

次の例は、デプロイされたリソースからプロパティを返す方法を示しています。

テンプレートに `outputs` セクションを追加します。 出力値には、パブリック IP アドレスの完全修飾ドメイン名が取得されます。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

名前にハイフンが含まれているプロパティを出力する必要がある場合は、ドット表記ではなく、名前を角かっこで囲みます。 たとえば、`.property-name` の代わりに `['property-name']` を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "user": {
      "user-name": "Test Person"
    }
  },
  "resources": [
  ],
  "outputs": {
    "nameResult": {
      "type": "string",
      "value": "[variables('user')['user-name']]"
    }
  }
}
```

## <a name="conditional-output"></a>条件付き出力

`condition` 要素を使用して、条件付きで値を返します。 通常、リソースを[条件付きでデプロイ](conditional-resource-deployment.md)した場合に条件付き出力を使用します。 次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

条件付き出力の簡単な例については、[条件付き出力テンプレート](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)に関する説明をご覧ください。

## <a name="dynamic-number-of-outputs"></a>動的な出力の数

場合により、テンプレートの作成時に、返す必要がある値のインスタンスの数が不明なシナリオもあります。 反復出力を使用すると、可変数の値を返すことができます。 `copy` 要素を追加して、出力を反復します。

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

詳細については、「[ARM テンプレートでの出力の反復処理](copy-outputs.md)」を参照してください。

## <a name="linked-templates"></a>リンク済みテンプレート

関連するテンプレートをデプロイするには、[リンク済みテンプレート](linked-templates.md)を使用します。 リンク済みテンプレートから出力値を取得するには、親テンプレートで [reference](template-functions-resource.md#reference) 関数を使用します。 親テンプレートの構文は次のとおりです。

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

次の例では、リンクされているテンプレートから値を取得することによってロード バランサーの IP アドレスを設定する方法を示します。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

プロパティ名にハイフンが含まれている場合は、ドット表記ではなく、名前を角かっこで囲みます。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

[入れ子になったテンプレート](linked-templates.md#nested-template) の出力セクションでは `reference` 関数を使用できません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。

[パブリック IP アドレス テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json)では、パブリック IP アドレスが作成され、リソース ID が出力されます。 [ロード バランサー テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json)は、前のテンプレートにリンクします。 ロード バランサーの作成時に出力内のリソース ID が使用されます。

## <a name="example-template"></a>テンプレートの例

次のテンプレートでは、リソースはデプロイされません。 さまざまな型の出力を返すいくつかの方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

## <a name="get-output-values"></a>出力値の取得

デプロイが成功すると、出力値はデプロイの結果で自動的に返されます。

デプロイ履歴から出力値を取得するには、スクリプトを使用できます。

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

* 出力に使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。