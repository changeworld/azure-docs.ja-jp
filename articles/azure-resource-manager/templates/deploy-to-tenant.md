---
title: リソースをテナントにデプロイする
description: Azure Resource Manager テンプレートでテナントのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: d72b4a63e564732a9a4baaf8b8cd94d0f165e12a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653337"
---
# <a name="create-resources-at-the-tenant-level"></a>テナント レベルでリソースを作成する

組織が成熟するにつれて、[ポリシー](../../governance/policy/overview.md)または[ロールベースのアクセス制御](../../role-based-access-control/overview.md)を定義し、Azure AD テナント全体に割り当てる必要性が生じることがあります。 テナント レベルのテンプレートを使用すると、宣言によってポリシーを適用し、グローバル レベルでロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

テナント レベルでは次のリソースの種類をデプロイできます。

* [deployments](/azure/templates/microsoft.resources/deployments) - 管理グループまたはサブスクリプションにデプロイする入れ子になったテンプレート用。
* [managementGroups](/azure/templates/microsoft.management/managementgroups)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>スキーマ

テナントのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
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

Azure CLI の場合は、[az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create) を使用します。

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Azure PowerShell の場合は [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

REST API の場合は、「[デプロイ - テナントのスコープでの作成または更新](/rest/api/resources/deployments/createorupdateattenantscope)」を使用します。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

テナント レベルのデプロイの場合は、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="use-template-functions"></a>テンプレート関数を使用する

テナント デプロイの場合、テンプレート関数を使用する際に重要な考慮事項がいくつかあります。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [subscription()](template-functions-resource.md#subscription) 関数は、サポートされて**いません**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) 関数がサポートされています。
* テナント レベルでデプロイされているリソースのリソース ID を取得するには、[tenantResourceId()](template-functions-resource.md#tenantresourceid) 関数を使用します。

  たとえば、ポリシー定義のリソース ID を取得するには、次を使用します。

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  返されるリソース ID の形式は次のとおりです。

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>管理グループの作成

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg)を使うと、管理グループを作成できます。

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

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment)を使うと、テナントのスコープでロールを割り当てることができます。

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

* ロールの割り当てについては、「[RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する](../../role-based-access-control/role-assignments-template.md)」をご覧ください。
* また、[サブスクリプション レベル](deploy-to-subscription.md)または[管理グループ レベル](deploy-to-management-group.md)でテンプレートをデプロイすることもできます。
