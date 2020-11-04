---
title: リソースをテナントにデプロイする
description: Azure Resource Manager テンプレートでテナントのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 854ccbd43509b6c0b5a04357844c78c32b7e6396
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668689"
---
# <a name="tenant-deployments-with-arm-templates"></a>ARM テンプレートを使用したテナントのデプロイ

組織が成熟するにつれて、[ポリシー](../../governance/policy/overview.md)または [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義し、Azure AD テナント全体に割り当てる必要性が生じることがあります。 テナント レベルのテンプレートを使用すると、宣言によってポリシーを適用し、グローバル レベルでロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をテナント レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure ポリシーでは、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

管理グループ、サブスクリプション、またはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

管理グループを作成するには、以下を使用します。

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

コストを管理するには、以下を使用します。

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>スキーマ

テナントのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
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

Azure CLI の場合は、[az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create) を使用します。

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell の場合は [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
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

管理グループにデプロイする際には、リソースを以下にデプロイできます。

* テナント
* テナント内の管理グループ
* subscriptions
* リソース グループ (入れ子になった 2 つのデプロイを使用)
* [拡張リソース](scope-extension-resources.md)はリソースに適用できます

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-tenant"></a>テナントへのスコープ

テンプレートのリソース セクション内で定義されたリソースは、テナントに適用されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>管理グループへのスコープ

テナント内で管理グループを対象にするには、入れ子になったデプロイを追加し、`scope` プロパティを指定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

また、テナント内のサブスクリプションを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

テナント内のサブスクリプションを対象にするには、入れ子になったデプロイと `subscriptionId` プロパティを使用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="10,18":::

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

テナント レベルのデプロイの場合は、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、 **azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="create-management-group"></a>管理グループの作成

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg)を使うと、管理グループを作成できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>ロールを割り当てる

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment)を使うと、テナントのスコープでロールを割り当てることができます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

* ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。
* また、[サブスクリプション レベル](deploy-to-subscription.md)または[管理グループ レベル](deploy-to-management-group.md)でテンプレートをデプロイすることもできます。
