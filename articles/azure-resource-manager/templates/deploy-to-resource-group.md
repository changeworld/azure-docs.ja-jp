---
title: リソースをリソース グループにデプロイする
description: Azure Resource Manager テンプレートでリソースをデプロイする方法について説明します。 複数のリソース グループを対象にする方法について説明します。
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681338"
---
# <a name="resource-group-deployments-with-arm-templates"></a>ARM テンプレートを使用したリソース グループへのデプロイ

この記事では、デプロイのスコープをリソース グループに限定する方法について説明します。 デプロイには、Azure Resource Manager テンプレート (ARM テンプレート) を使用します。 この記事では、デプロイ操作でリソース グループを超えてスコープを拡張する方法についても説明します。

## <a name="supported-resources"></a>サポートされているリソース

ほとんどのリソースは、リソース グループにデプロイできます。 使用可能なリソースのリストについては、[ARM テンプレートのリファレンス](/azure/templates)を参照してください。

## <a name="schema"></a>スキーマ

テンプレートでは、以下のスキーマを使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

パラメーター ファイルの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>デプロイ コマンド

リソース グループにデプロイするには、リソース グループのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell デプロイ コマンドには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure portal でリソースをデプロイする](deploy-portal.md)
* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [ARM テンプレートと Azure Resource Manager REST API を使用してリソースをデプロイする](deploy-rest.md)
* [デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](deploy-to-azure-button.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>デプロイのスコープ

リソース グループにデプロイする際には、リソースを以下にデプロイできます。

* 操作のターゲット リソース グループ
* 同じサブスクリプションまたは他のサブスクリプション内の他のリソース グループ
* [拡張リソース](scope-extension-resources.md)はリソースに適用できます

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-target-resource-group"></a>ターゲット リソース グループにスコープを設定する

リソースをターゲット リソースにデプロイするには、テンプレートのリソース セクションに目的のリソースを追加します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>同じサブスクリプション内のリソース グループにスコープを設定する

同じサブスクリプション内の別のリソース グループにリソースをデプロイするには、入れ子になったデプロイを追加して、`resourceGroup` プロパティを含めます。 サブスクリプション ID またはリソース グループを指定しない場合は、親テンプレートのサブスクリプションとリソース グループが使用されます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。

次の例では、入れ子になったデプロイは `demoResourceGroup` という名前のリソース グループを対象としています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>別のサブスクリプション内のリソース グループにスコープを設定する

別のサブスクリプション内のリソース グループにリソースをデプロイするには、入れ子になったデプロイを追加し、 `subscriptionId` と `resourceGroup` の各プロパティを含めます。 次の例では、入れ子になったデプロイは `demoResourceGroup` という名前のリソース グループを対象としています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>リソース グループをまたぐ場合

1 つの ARM テンプレートで複数のリソース グループにデプロイできます。 親テンプレートとは異なるリソース グループをターゲットにするには、[入れ子になったテンプレートまたはリンクされたテンプレート](linked-templates.md)を使用します。 デプロイ リソースの種類に、入れ子になったテンプレートのデプロイ対象のサブスクリプション ID およびリソース グループの値を指定します。 リソース グループは、異なるサブスクリプションに存在していても構いません。

> [!NOTE]
> 単一のデプロイで **800 のリソース グループ** にデプロイできます。 通常、この制限は、親テンプレートに対しては 1 つのリソース グループを、入れ子になったデプロイまたはリンクされたデプロイでは最大 799 のリソース グループを指定してデプロイできることを意味します。 ただし、親テンプレートが入れ子になったテンプレートまたはリンクされたテンプレートのみを含んでいて、親テンプレート自体はリソースをデプロイしない場合は、入れ子になったデプロイまたはリンクされたデプロイで最大 800 のリソース グループを含めることができます。

次の例では、2 つのストレージ アカウントをデプロイします。 最初のストレージ アカウントは、デプロイ操作で指定されたリソース グループにデプロイされます。 2 番目のストレージ アカウントは、`secondResourceGroup` および `secondSubscriptionID` パラメーターで指定されたリソース グループにデプロイされます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

`resourceGroup` を存在しないリソース グループの名前に設定すると、デプロイは失敗します。

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

2 つのストレージ アカウントを **同じ サブスクリプション** 内の 2 つのリソース グループにデプロイするには、次を使用します。

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

2 つのストレージ アカウントを **2 つのサブスクリプション** にデプロイするには、次を使用します。

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

2 つのストレージ アカウントを **同じ サブスクリプション** 内の 2 つのリソース グループにデプロイするには、次を使用します。

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

2 つのストレージ アカウントを **2 つのサブスクリプション** にデプロイするには、次を使用します。

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

## <a name="next-steps"></a>次のステップ

* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
