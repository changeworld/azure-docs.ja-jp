---
title: 管理グループにリソースをデプロイする
description: Azure Resource Manager テンプレートを使用して、管理グループのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 084ab69f463334569d37efd9187bfe587bfc524d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668933"
---
# <a name="management-group-deployments-with-arm-templates"></a>ARM テンプレートを使用した管理グループへのデプロイ

成熟している組織では、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイして、管理グループ レベルでリソースを作成することができます。 たとえば、場合によっては、管理グループの[ポリシー](../../governance/policy/overview.md)や [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義して、割り当てる必要があります。 管理グループ レベルのテンプレートがあれば、宣言という方法を用いて管理グループ レベルでポリシーを適用し、ロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類を管理グループ レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure Blueprints では、以下を使用します。

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure ポリシーでは、以下を使用します。

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

## <a name="schema"></a>スキーマ

管理グループのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

## <a name="deployment-commands"></a>デプロイ コマンド

管理グループにデプロイするには、管理グループのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create) を使用します。

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

* [ARM テンプレートと Azure portal でリソースをデプロイする](deploy-portal.md)
* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [ARM テンプレートと Azure Resource Manager REST API を使用してリソースをデプロイする](deploy-rest.md)
* [デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](deploy-to-azure-button.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>デプロイのスコープ

管理グループにデプロイする際には、リソースを以下にデプロイできます。

* 操作のターゲット管理グループ
* テナント内の別の管理グループ
* 管理グループ内のサブスクリプション
* 管理グループ内のリソース グループ (入れ子になった 2 つのデプロイを使用)
* [拡張リソース](scope-extension-resources.md)はリソースに適用できます

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-target-management-group"></a>ターゲット管理グループへのスコープ

テンプレートのリソース セクション内で定義されたリソースは、デプロイ コマンドで指定された管理グループに適用されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>別の管理グループへのスコープ

別の管理グループを対象にするには、入れ子になったデプロイを追加し、`scope` プロパティを指定します。 `scope` プロパティを `Microsoft.Management/managementGroups/<mg-name>` という形式の値に設定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

また、管理グループ内のサブスクリプションを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

管理グループ内のサブスクリプションを対象にするには、入れ子になったデプロイと `subscriptionId` プロパティを使用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

そのサブスクリプション内のリソース グループを対象にするには、入れ子になった 2 つのデプロイを追加します。 1 つ目は、リソース グループを持つサブスクリプションを対象とします。 2 つ目は、`resourceGroup` プロパティを設定して、リソース グループを対象とします。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

サブスクリプション内にリソース グループを作成し、そのリソース グループにストレージ アカウントをデプロイするために、管理グループのデプロイを使用するには、「[サブスクリプションとリソース グループにデプロイする](#deploy-to-subscription-and-resource-group)」を参照してください。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

管理グループ レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、 **azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="azure-policy"></a>Azure Policy

管理グループにデプロイされているカスタム ポリシー定義は、管理グループの拡張機能です。 カスタム ポリシー定義の ID を取得するには、[extensionResourceId()](template-functions-resource.md#extensionresourceid) 関数を使用します。 組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みポリシー定義の ID を取得するには、[tenantResourceId](template-functions-resource.md#tenantresourceid) 関数を使用します。

次の例は、管理グループ レベルでポリシーを[定義](../../governance/policy/concepts/definition-structure.md)して割り当てる方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>サブスクリプションとリソース グループにデプロイする

管理グループ レベルのデプロイから、管理グループ内のサブスクリプションを対象にすることができます。 次の例では、サブスクリプション内にリソース グループを作成し、ストレージ アカウントをそのリソース グループにデプロイします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

* ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* [サブスクリプション レベル](deploy-to-subscription.md)と[テナント レベル](deploy-to-tenant.md)でテンプレートをデプロイすることもできます。
