---
title: Bicep を使用してリソースをリソース グループにデプロイする
description: Bicep ファイルでリソースをデプロイする方法について説明します。 複数のリソース グループを対象にする方法について説明します。
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 57e6814a8d7acd5c35995e037c2fd0c1c6804819
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426942"
---
# <a name="resource-group-deployments-with-bicep-files"></a>Bicep ファイルを使用したリソース グループへのデプロイ

この記事では、リソース グループにデプロイするときに Bicep を使用してスコープを設定する方法について説明します。

## <a name="supported-resources"></a>サポートされているリソース

ほとんどのリソースは、リソース グループにデプロイできます。 使用可能なリソースのリストについては、[ARM テンプレートのリファレンス](/azure/templates)を参照してください。

## <a name="set-scope"></a>セット スコープ

既定では、Bicep ファイルは、リソース グループにスコープが設定されています。 スコープを明示的に設定する場合は、次のコマンドを使用します。

```bicep
targetScope = 'resourceGroup'
```

ただし、既定で使用されているため、ターゲット スコープをリソース グループに設定する必要はありません。

## <a name="deployment-commands"></a>デプロイ コマンド

リソース グループにデプロイするには、リソース グループのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-file main.bicep \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell デプロイ コマンドには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile main.bicep `
  -storageAccountType Standard_GRS `
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>デプロイのスコープ

リソース グループにデプロイする際には、リソースを以下にデプロイできます。

* デプロイ操作のターゲット リソース グループ
* 同じサブスクリプションまたは他のサブスクリプション内の他のリソース グループ
* テナント内の任意のサブスクリプション
* リソース グループのテナント

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-target-resource-group"></a>ターゲット リソース グループにスコープを設定する

リソースをターゲット リソース グループにデプロイするには、Bicep ファイルにそれらのリソースを追加します。

```bicep
// resource deployed to target resource group
resource exampleResource 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

テンプレートの例については、「[ターゲット リソース グループにデプロイする](#deploy-to-target-resource-group)」を参照してください。

### <a name="scope-to-different-resource-group"></a>異なるリソース グループにスコープを設定する

ターゲット リソース グループではないリソース グループにリソースをデプロイするには、[モジュール](modules.md)を追加します。 [resourceGroup 関数](bicep-functions-scope.md#resourcegroup)を使用して、そのモジュールに対して `scope` プロパティを設定します。

リソース グループが別のサブスクリプションにある場合は、サブスクリプション ID とリソース グループの名前を指定します。 リソース グループが現在のデプロイと同じサブスクリプション内にある場合は、リソース グループの名前のみを指定します。 [resourceGroup 関数](bicep-functions-scope.md#resourcegroup)でサブスクリプションを指定しない場合は、現在のサブスクリプションが使用されます。

次の例には、別のサブスクリプション内のリソース グループを対象とするモジュールが示されています。

```bicep
param otherResourceGroup string
param otherSubscriptionID string

// module deployed to different subscription and resource group
module exampleModule 'module.bicep' = {
  name: 'otherSubAndRG'
  scope: resourceGroup(otherSubscriptionID, otherResourceGroup)
}
```

次の例には、同じサブスクリプション内のリソース グループを対象とするモジュールが示されています。

```bicep
param otherResourceGroup string

// module deployed to resource group in the same subscription
module exampleModule 'module.bicep' = {
  name: 'otherRG'
  scope: resourceGroup(otherResourceGroup)
}
```

テンプレートの例については、「[複数のリソースグループにデプロイする](#deploy-to-multiple-resource-groups)」を参照してください。

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

サブスクリプションにリソースをデプロイするには、モジュールを追加します。 [subscription 関数](bicep-functions-scope.md#subscription)を使用して、`scope` プロパティを設定します。

現在のサブスクリプションにデプロイするには、パラメーターを指定せずに subscription 関数を使用します。

```bicep

// module deployed at subscription level
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

別のサブスクリプションにデプロイするには、そのサブスクリプション ID を subscription 関数のパラメーターとして指定します。

```bicep
param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(otherSubscriptionID)
}
```

テンプレートの例については、「[リソース グループの作成](#create-resource-group)」を参照してください。

### <a name="scope-to-tenant"></a>テナントへのスコープ

テナントでリソースを作成するには、モジュールを追加します。 [tenant 関数](bicep-functions-scope.md#tenant)を使用して、その `scope` プロパティを設定します。

テンプレートをデプロイするユーザーには、[テナントでデプロイするための必要なアクセス権が必要です](deploy-to-tenant.md#required-access)。

次の例には、テナントにデプロイするモジュールが含まれています。

```bicep
// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

モジュールを使用する代わりに、一部のリソースの種類に対してスコープを `tenant()` に設定できます。 次の例では、テナントに管理グループをデプロイします。

```bicep
param mgName string = 'mg-${uniqueString(newGuid())}'

// ManagementGroup deployed at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

詳細については、「[管理グループ](deploy-to-management-group.md#management-group)」を参照してください。

## <a name="deploy-to-target-resource-group"></a>ターゲット リソース グループにデプロイする

リソースをターゲット リソース グループにデプロイするには、テンプレートの`resources` セクションにそれらのリソースを定義します。 次のテンプレートでは、デプロイ操作で指定されているリソース グループにストレージ アカウントが作成されます。

:::code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep":::

## <a name="deploy-to-multiple-resource-groups"></a>複数のリソース グループにデプロイする

1 つの Bicep ファイルで複数のリソース グループにデプロイできます。

> [!NOTE]
> 単一のデプロイで **800 のリソース グループ** にデプロイできます。 通常、この制限は、親テンプレートに対しては 1 つのリソース グループを、入れ子になったデプロイまたはリンクされたデプロイでは最大 799 のリソース グループを指定してデプロイできることを意味します。 ただし、親テンプレートが入れ子になったテンプレートまたはリンクされたテンプレートのみを含んでいて、親テンプレート自体はリソースをデプロイしない場合は、入れ子になったデプロイまたはリンクされたデプロイで最大 800 のリソース グループを含めることができます。

次の例では、2 つのストレージ アカウントをデプロイします。 最初のストレージ アカウントは、デプロイ操作で指定されたリソース グループにデプロイされます。 2 番目のストレージ アカウントは、`secondResourceGroup` および `secondSubscriptionID` パラメーターで指定されたリソース グループにデプロイされます。

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondStorageLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
var secondStorageName = '${storagePrefix}${uniqueString(secondSubscriptionID, secondResourceGroup)}'

module firstStorageAcct 'storage.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

module secondStorageAcct 'storage.bicep' = {
  name: 'storageModule2'
  scope: resourceGroup(secondSubscriptionID, secondResourceGroup)
  params: {
    storageLocation: secondStorageLocation
    storageName: secondStorageName
  }
}
```

どちらのモジュールも、**storage.bicep** という名前の同じ Bicep ファイルを使用します。

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="create-resource-group"></a>リソース グループの作成

リソース グループのデプロイから、サブスクリプションのレベルに切り替えて、リソース グループを作成することができます。 次のテンプレートでは、ストレージ アカウントをターゲット リソース グループにデプロイし、指定されたサブスクリプションに新しいリソース グループを作成します。

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

// resource deployed to target resource group
module firstStorageAcct 'storage2.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

// module deployed to subscription
module newRG 'resourceGroup.bicep' = {
  name: 'newResourceGroup'
  scope: subscription(secondSubscriptionID)
  params: {
    resourceGroupName: secondResourceGroup
    resourceGroupLocation: secondLocation
  }
}
```

前の例では、新しいリソース グループを作成するモジュールに、次の Bicep ファイルを使用しています。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

## <a name="next-steps"></a>次のステップ

他のスコープについて確認するには、以下を参照してください。

* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループへのデプロイ](deploy-to-management-group.md)
* [テナントへのデプロイ](deploy-to-tenant.md)
