---
title: サブスクリプションおよびリソース グループにまたがってリソースをデプロイする
description: デプロイ時に複数の Azure サブスクリプションとリソース グループを対象にする方法について説明します。
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650625"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする

Resource Manager を使用すると、単一のデプロイで複数のリソース グループにデプロイできます。 入れ子になったテンプレートを使用して、デプロイ操作のリソース グループとは異なるリソース グループを指定します。 リソース グループは、異なるサブスクリプションに存在していても構いません。

> [!NOTE]
> 単一のデプロイで **800 のリソース グループ**にデプロイできます。 通常、この制限は、親テンプレートに対しては 1 つのリソース グループを、入れ子になったデプロイまたはリンクされたデプロイでは最大 799 のリソース グループを指定してデプロイできることを意味します。 ただし、親テンプレートが入れ子になったテンプレートまたはリンクされたテンプレートのみを含んでいて、親テンプレート自体はリソースをデプロイしない場合は、入れ子になったデプロイまたはリンクされたデプロイで最大 800 のリソース グループを含めることができます。

## <a name="specify-subscription-and-resource-group"></a>サブスクリプションとリソース グループを指定する

親テンプレートとは異なるリソース グループをターゲットにするには、[入れ子になったテンプレートまたはリンクされたテンプレート](linked-templates.md)を使用します。 デプロイ リソースの種類に、入れ子になったテンプレートのデプロイ対象のサブスクリプション ID およびリソース グループの値を指定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

サブスクリプション ID またはリソース グループを指定しない場合は、親テンプレートのサブスクリプションとリソース グループが使用されます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。

テンプレートをデプロイするアカウントには、指定されたサブスクリプション ID にデプロイする権限が必要です。 指定したサブスクリプションが別の Azure Active Directory テナントに存在する場合は、[別のディレクトリからゲスト ユーザーを追加する](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)必要があります。

次の例では、2 つのストレージ アカウントをデプロイします。 最初のストレージ アカウントは、デプロイ操作で指定されたリソース グループにデプロイされます。 2 番目のストレージ アカウントは、`secondResourceGroup` および `secondSubscriptionID` パラメーターで指定されたリソース グループにデプロイされます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

`resourceGroup` を存在しないリソース グループの名前に設定すると、デプロイは失敗します。

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

2 つのストレージ アカウントを**同じ サブスクリプション**内の 2 つのリソース グループにデプロイするには、次を使用します。

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

2 つのストレージ アカウントを **2 つのサブスクリプション**にデプロイするには、次を使用します。

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

2 つのストレージ アカウントを**同じ サブスクリプション**内の 2 つのリソース グループにデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

2 つのストレージ アカウントを **2 つのサブスクリプション**にデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>関数を使用する

[resourceGroup()](template-functions-resource.md#resourcegroup) および [subscription()](template-functions-resource.md#subscription) 関数は、テンプレートをどのように指定するかに基づいて異なった方法で解決されます。 外部テンプレートにリンクする場合、これらの関数は、常にそのテンプレートのスコープに解決されます。 親テンプレート内でテンプレートを入れ子にする場合は、`expressionEvaluationOptions` プロパティを使用して、これらの関数が親テンプレートと入れ子になったテンプレートのどちらのリソース グループおよびサブスクリプションに解決されるかを指定します。 入れ子になったテンプレートのスコープに解決されるようにするには、このプロパティを `inner` に設定します。 親テンプレートのスコープに解決されるようにするには、このプロパティを `outer` に設定します。

次の表は、これらの関数が親と埋め込みのどちらのリソース グループおよびサブスクリプションに解決されるかを示しています。

| テンプレートの種類 | Scope | 解像度 |
| ------------- | ----- | ---------- |
| 入れ子        | outer (既定) | 親リソース グループ |
| 入れ子        | inner | サブ リソース グループ |
| リンク済み        | 該当なし   | サブ リソース グループ |

次の[テンプレートの例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)は、次のものを示しています。

* 既定の (outer) スコープを持つ入れ子になったテンプレート
* inner スコープを持つ入れ子になったテンプレート
* リンク済みテンプレート

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前の例からの出力は次のようになります。

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前の例からの出力は次のようになります。

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>次のステップ

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](secure-template-with-sas-token.md)」を参照してください。
