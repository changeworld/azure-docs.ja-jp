---
title: Bicep を使用してサブスクリプションにリソースをデプロイする
description: Azure サブスクリプションのスコープにリソースをデプロイする Bicep ファイルを作成する方法について説明します。 リソース グループを作成する方法を示します。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: df9938e209e163b19e666a26dec4176e83fc79e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793742"
---
# <a name="subscription-deployments-with-bicep-files"></a>Bicep ファイルを使用したサブスクリプションのデプロイ

この記事では、サブスクリプションにデプロイするときに Bicep でスコープを設定する方法について説明します。

リソースの管理を簡略化するには、Azure サブスクリプションのレベルでリソースをデプロイできます。 たとえば、[ポリシー](../../governance/policy/overview.md)および [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) をサブスクリプションにデプロイすると、これらのリソースがサブスクリプション全体に適用されます。 サブスクリプション内にリソース グループを作成し、サブスクリプションのリソース グループにリソースをデプロイすることもできます。

> [!NOTE]
> サブスクリプション レベルのデプロイでは、800 の異なるリソース グループにデプロイできます。

### <a name="microsoft-learn"></a>Microsoft Learn

デプロイ スコープの詳細およびハンズオン ガイダンスについては、**Microsoft Learn** の「[Bicep を使用してサブスクリプション、管理グループ、テナントにリソースをデプロイする](/learn/modules/deploy-resources-scopes-bicep/)」を参照してください。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をサブスクリプション レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure Blueprints では、以下を使用します。

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure ポリシーでは、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

リソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

新しいリソース グループを作成する場合は、以下を使用します。

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

サブスクリプションを管理する場合は、以下を使用します。

* [Advisor 構成](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [Change Analysis プロファイル](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tags](/azure/templates/microsoft.resources/tags)

その他に、以下の種類がサポートされています。

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>セット スコープ

スコープをサブスクリプションに設定するには、以下を使用します。

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>デプロイ コマンド

サブスクリプションにデプロイするには、サブスクリプション レベルのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell デプロイ コマンドについては、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) または別名 `New-AzSubscriptionDeployment` を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

サブスクリプション レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。 [管理グループ](deploy-to-management-group.md)および[テナント](deploy-to-tenant.md)のデプロイにも場所が必要です。 [リソース グループ](deploy-to-resource-group.md)のデプロイの場合、リソース グループの場所を使用してデプロイ データを格納します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、_main.json_ という名前のテンプレートをデプロイすると、**main** という既定のデプロイ名が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 たとえば、**centralus** で **deployment1** という名前のサブスクリプションのデプロイを作成した場合、後で **deployment1** という名前の別のデプロイを **westus** の場所に作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

サブスクリプションにデプロイする際には、リソースを以下にデプロイできます。

* 操作のターゲット サブスクリプション
* テナント内の任意のサブスクリプション
* そのサブスクリプション内または他のサブスクリプション内のリソース グループ
* そのサブスクリプションのテナント

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

対象となるサブスクリプションにリソースをデプロイするには、`resource` キーワードでそれらのリソースを追加します。

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

サブスクリプションへのデプロイの例については、「[リソース グループを作成する](#create-resource-groups)」と「[ポリシー定義を割り当てる](#assign-policy-definition)」を参照してください。

操作のサブスクリプションとは異なるサブスクリプションにリソースをデプロイするには、[モジュール](modules.md)を追加します。 [subscription 関数](bicep-functions-scope.md#subscription)を使用して、`scope` プロパティを設定します。 デプロイ先のサブスクリプションの ID に `subscriptionId` プロパティを指定します。

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

リソースをサブスクリプション内のリソース グループにデプロイするには、モジュールを追加し、その `scope` プロパティを設定します。 リソース グループが既に存在する場合は、[resourceGroup 関数](bicep-functions-scope.md#resourcegroup)を使用してスコープの値を設定します。 リソース グループ名を指定します。

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

同じ Bicep ファイルでリソース グループを作成する場合は、リソース グループのシンボリック名を使用してスコープの値を設定します。 スコープをシンボリック名に設定する例については、「[リソース グループとリソースを作成する](#create-resource-group-and-resources)」を参照してください。

### <a name="scope-to-tenant"></a>テナントへのスコープ

テナントでリソースを作成するには、モジュールを追加します。 [tenant 関数](bicep-functions-scope.md#tenant)を使用して、その `scope` プロパティを設定します。

テンプレートをデプロイするユーザーには、[テナントでデプロイするための必要なアクセス権が必要です](deploy-to-tenant.md#required-access)。

次の例には、テナントにデプロイするモジュールが含まれています。

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

モジュールを使用する代わりに、一部のリソースの種類に対してスコープを `tenant()` に設定できます。 次の例では、テナントに管理グループをデプロイします。

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

詳細については、「[管理グループ](deploy-to-management-group.md#management-group)」を参照してください。

## <a name="resource-groups"></a>リソース グループ

### <a name="create-resource-groups"></a>リソース グループを作成する

リソース グループを作成するには、リソース グループの名前と場所を指定して [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) リソースを定義します。

次の例では、空のリソース グループを作成します。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>リソース グループとリソースを作成する

リソース グループを作成してそれにリソースをデプロイするには、モジュールを追加します。 モジュールには、リソース グループにデプロイするリソースが含まれます。 モジュールのスコープを、作成するリソース グループのシンボリック名に設定します。 最大 800 のリソース グループにデプロイできます。

次の例では、リソース グループを作成し、ストレージ アカウントをリソース グループにデプロイします。 モジュールの `scope` プロパティが `newRG` に設定されていることに注意してください。これは、作成されているリソース グループのシンボリック名です。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

モジュールでは、次のような内容で **storage.bicep** という名前の Bicep ファイルを使用します。

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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>ポリシー定義を割り当てる

次の例は、既存のポリシー定義をサブスクリプションに割り当てます。 ポリシー定義がパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシー定義がパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>ポリシー定義を作成して割り当てる

ポリシー定義を同じ Bicep ファイルで[定義](../../governance/policy/concepts/definition-structure.md)して割り当てることができます。

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>アクセス制御

ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。

次の例では、リソース グループを作成し、そのグループにロックを適用して、プリンシパルにロールを割り当てます。

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

次の例では、ロックを適用するモジュールを示します。

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

次の例では、ロールを割り当てるモジュールを示します。

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>次のステップ

他のスコープについて確認するには、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [管理グループへのデプロイ](deploy-to-management-group.md)
* [テナントへのデプロイ](deploy-to-tenant.md)
