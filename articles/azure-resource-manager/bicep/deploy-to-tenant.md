---
title: Bicep を使用してテナントにリソースをデプロイする
description: Bicep ファイルでテナント スコープにリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: fe5b8a1a17af47570bb2399172866a1e720b0bab
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793723"
---
# <a name="tenant-deployments-with-bicep-file"></a>Bicep ファイルを使用したテナントのデプロイ

組織が成熟するにつれて、[ポリシー](../../governance/policy/overview.md)または [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義し、Azure AD テナント全体に割り当てる必要性が生じることがあります。 テナント レベルのテンプレートを使用すると、宣言によってポリシーを適用し、グローバル レベルでロールを割り当てることができます。

### <a name="microsoft-learn"></a>Microsoft Learn

デプロイ スコープの詳細およびハンズオン ガイダンスについては、**Microsoft Learn** の「[Bicep を使用してサブスクリプション、管理グループ、テナントにリソースをデプロイする](/learn/modules/deploy-resources-scopes-bicep/)」を参照してください。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をテナント レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

管理グループ、サブスクリプション、またはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

管理グループを作成するには、以下を使用します。

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

サブスクリプションを作成するには、以下を使用します。

* [aliases](/azure/templates/microsoft.subscription/aliases)

コストを管理するには、以下を使用します。

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

ポータルを構成するには、以下を使用します。

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

組み込みのポリシー定義はテナントレベルのリソースですが、カスタム ポリシー定義をテナントにデプロイすることはできません。 組み込みのポリシー定義をリソースに割り当てる例については、「[Tenantresourceid の例](./bicep-functions-resource.md#tenantresourceid)」を参照してください。

## <a name="set-scope"></a>セット スコープ

スコープをテナントに設定するには、次のようにします。

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>必要なアクセス

テンプレートをデプロイするプリンシパルには、テナントのスコープでリソースを作成するためのアクセス許可が必要です。 このプリンシパルには、デプロイ アクション (`Microsoft.Resources/deployments/*`) を実行し、テンプレートに定義されているリソースを作成するためのアクセス許可が必要です。 たとえば、管理グループを作成するには、プリンシパルにテナントのスコープの共同作成者アクセス許可が必要です。 ロールの割り当てを作成するには、プリンシパルに所有者アクセス許可が必要です。

Azure Active Directory の全体管理者には、ロールを割り当てるアクセス許可が自動的に付与されるわけではありません。 テナントのスコープでテンプレートのデプロイを有効にするには、全体管理者が次の手順を実行する必要があります。

1. 全体管理者がロールを割り当てることができるように、アカウントのアクセス権を昇格させます。 詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

1. テンプレートをデプロイする必要があるプリンシパルに、所有者または共同作成者を割り当てます。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

これで、テンプレートをデプロイするために必要なアクセス許可がプリンシパルに付与されました。

## <a name="deployment-commands"></a>デプロイ コマンド

テナントのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドとは異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create) を使用します。

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell の場合は [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

テナント レベルのデプロイの場合は、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。 [サブスクリプション](deploy-to-subscription.md)および[管理グループ](deploy-to-management-group.md)のデプロイにも場所が必要です。 [リソース グループ](deploy-to-resource-group.md)のデプロイの場合、リソース グループの場所を使用してデプロイ データを格納します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、_main.bicep_ という名前のファイルをデプロイすると、**main** という既定のデプロイ名が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 たとえば、**centralus** で **deployment1** という名前のテナントのデプロイを作成した場合、後で **deployment1** という名前の別のデプロイを **westus** の場所に作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

テナントにデプロイする際には、リソースを以下にデプロイできます。

* テナント
* テナント内の管理グループ
* subscriptions
* リソース グループ

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-tenant"></a>テナントへのスコープ

Bicep ファイルで定義されたリソースは、テナントに適用されます。

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>管理グループへのスコープ

テナント内の管理グループを対象とするには、[モジュール](modules.md)を追加します。 [managementGroup 関数](bicep-functions-scope.md#managementgroup)を使用して、`scope` プロパティを設定します。 管理グループ名を指定します。

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

テナント内のサブスクリプションを対象とするには、モジュールを追加します。 [subscription 関数](bicep-functions-scope.md#subscription)を使用して、`scope` プロパティを設定します。 サブスクリプション ID を入力します。

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

テナント内のリソース グループを対象とするには、モジュールを追加します。 [resourceGroup 関数](bicep-functions-scope.md#resourcegroup)を使用して、`scope` プロパティを設定します。 サブスクリプション ID とリソース グループ名を指定します。

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>管理グループの作成

次のテンプレートを使うと、管理グループを作成できます。

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

テナントにデプロイするためのアクセス許可がアカウントにない場合でも、別のスコープにデプロイすることによって管理グループを作成できます。 詳細については、「[管理グループ](deploy-to-management-group.md#management-group)」を参照してください。

## <a name="assign-role"></a>ロールを割り当てる

次のテンプレートを使うと、テナントのスコープでロールを割り当てることができます。

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>次のステップ

他のスコープについて確認するには、以下を参照してください。

* [リソース グループのデプロイ](deploy-to-resource-group.md)
* [サブスクリプションへのデプロイ](deploy-to-subscription.md)
* [管理グループのデプロイ](deploy-to-management-group.md)
