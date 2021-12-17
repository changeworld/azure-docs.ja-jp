---
title: Bicep を使用して管理グループにリソースをデプロイする
description: 管理グループのスコープでリソースをデプロイする Bicep ファイルを作成する方法について説明します。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 8e198f923e864b0919f20cb4d0ef6579bb375ec4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178324"
---
# <a name="management-group-deployments-with-bicep-files"></a>Bicep ファイルを使用した管理グループへのデプロイ

この記事では、管理グループにデプロイするときに Bicep を使用してスコープを設定する方法について説明します。

成熟している組織では、Bicep ファイルをデプロイして、管理グループ レベルでリソースを作成することができます。 たとえば、場合によっては、管理グループの[ポリシー](../../governance/policy/overview.md)や [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義して、割り当てる必要があります。 管理グループ レベルのテンプレートがあれば、宣言という方法を用いて管理グループ レベルでポリシーを適用し、ロールを割り当てることができます。

### <a name="microsoft-learn"></a>Microsoft Learn

デプロイ スコープの詳細およびハンズオン ガイダンスについては、**Microsoft Learn** の「[Bicep を使用してサブスクリプション、管理グループ、テナントにリソースをデプロイする](/learn/modules/deploy-resources-scopes-bicep/)」を参照してください。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類を管理グループ レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure Blueprints では、以下を使用します。

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure Policy では、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

サブスクリプションまたはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

リソースを管理する場合は、以下を使用します。

* [tags](/azure/templates/microsoft.resources/tags)

管理グループは、テナント レベルのリソースです。 ただし、新しい管理グループのスコープをテナントに設定することにより、管理グループのデプロイで管理グループを作成できます。 「[管理グループ](#management-group)」を参照してください。

## <a name="set-scope"></a>セット スコープ

スコープを管理グループに設定するには、次を使用します。

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>デプロイ コマンド

管理グループにデプロイするには、管理グループのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) を使用します。

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell では、[New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment) を使用します。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

管理グループ レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。 [サブスクリプション](deploy-to-subscription.md)および[テナント](deploy-to-tenant.md)のデプロイにも場所が必要です。 [リソース グループ](deploy-to-resource-group.md)のデプロイの場合、リソース グループの場所を使用してデプロイ データを格納します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、_main.bicep_ という名前のテンプレートをデプロイすると、**main** という既定のデプロイ名が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 たとえば、**centralus** で **deployment1** という名前の管理グループのデプロイを作成した場合、後で **deployment1** という名前の別のデプロイを **westus** の場所に作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

管理グループにデプロイする際には、リソースを以下にデプロイできます。

* 操作のターゲット管理グループ
* テナント内の別の管理グループ
* 管理グループ内のサブスクリプション
* 管理グループ内のリソース グループ
* リソース グループのテナント

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

### <a name="scope-to-management-group"></a>管理グループへのスコープ

対象となる管理グループにリソースをデプロイするには、`resource` キーワードを使用してこれらのリソースを追加します。

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

別の管理グループを対象とするには、[モジュール](modules.md)を追加します。 [managementGroup 関数](bicep-functions-scope.md#managementgroup)を使用して、`scope` プロパティを設定します。 管理グループ名を指定します。

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

また、管理グループ内のサブスクリプションを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

管理グループ内のサブスクリプションを対象とするには、モジュールを追加します。 [subscription 関数](bicep-functions-scope.md#subscription)を使用して、`scope` プロパティを設定します。 サブスクリプション ID を指定します。

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

また、管理グループ内のリソース グループを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

管理グループ内のリソース グループを対象とするには、モジュールを追加します。 [resourceGroup 関数](bicep-functions-scope.md#resourcegroup)を使用して、`scope` プロパティを設定します。  サブスクリプション ID とリソース グループ名を指定します。

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>テナントへのスコープ

テナントでリソースを作成するには、モジュールを追加します。 [tenant 関数](bicep-functions-scope.md#tenant)を使用して、その `scope` プロパティを設定します。 テンプレートをデプロイするユーザーには、[テナントでデプロイするための必要なアクセス権が必要です](deploy-to-tenant.md#required-access)。

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

または、管理グループなどの一部のリソースの種類に対して、スコープを `/` に設定することもできます。 新しい管理グループの作成については、次のセクションで説明します。

## <a name="management-group"></a>管理グループ

管理グループのデプロイで管理グループを作成するには、スコープをテナントに設定する必要があります。

次の例では、ルート管理グループに新しい管理グループを作成します。

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

次の例では、デプロイの対象となる管理グループに新しい管理グループを作成します。 [管理グループの機能](bicep-functions-scope.md#managementgroup)を使用します。

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>サブスクリプション

ARM テンプレートを使用して、管理グループに新しい Azure サブスクリプションを作成するには、以下を参照してください。

* [プログラムで Azure Enterprise Agreement サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [プログラムで Microsoft 顧客契約の Azure サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [プログラムで Microsoft Partner Agreement の Azure サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

既存の Azure サブスクリプションを新しい管理グループに移動するテンプレートをデプロイする場合は、「[ARM テンプレートでのサブスクリプションの移動](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)」を参照してください

## <a name="azure-policy"></a>Azure Policy

管理グループにデプロイされているカスタム ポリシー定義は、管理グループの拡張機能です。 カスタム ポリシー定義の ID を取得するには、[extensionResourceId()](./bicep-functions-resource.md#extensionresourceid) 関数を使用します。 組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みポリシー定義の ID を取得するには、[tenantResourceId()](./bicep-functions-resource.md#tenantresourceid) 関数を使用します。

次の例は、管理グループ レベルでポリシーを[定義](../../governance/policy/concepts/definition-structure.md)して割り当てる方法を示しています。

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>次の手順

他のスコープについて確認するには、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [テナントへのデプロイ](deploy-to-tenant.md)
