---
title: Azure サブスクリプションにリソースをデプロイする | Microsoft Docs
description: サブスクリプションのスコープでリソースをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1d281ebe80c6089c559cfaa77f4875a856566092
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079380"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Azure サブスクリプションにリソースをデプロイする

通常、リソースは Azure サブスクリプションのリソース グループにデプロイします。 ただし、一部のリソースは Azure サブスクリプションのレベルでデプロイできます。 これらのリソースはサブスクリプション全体に適用されます。 [ポリシー](../azure-policy/azure-policy-introduction.md)、[ロール ベースのアクセス制御](../role-based-access-control/overview.md)、[Azure Security Center](../security-center/security-center-intro.md) などのサービスは、リソース グループ レベルではなく、サブスクリプション レベルで適用したほうがよい場合があります。

この記事では、Azure CLI と PowerShell を使用してテンプレートをデプロイします。

## <a name="name-and-location-for-deployment"></a>デプロイの名前と場所

サブスクリプションへのデプロイ時に、デプロイの場所を指定する必要があります。 また、デプロイの名前も指定することができます。 デプロイの名前を指定しない場合は、テンプレートの名前がデプロイ名として使用されます。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

サブスクリプション レベルのデプロイの場所は、変更できません。 ある場所でデプロイを作成しようとしても、同じ名前で場所が異なるデプロイが既にある場合は、作成することができません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="using-template-functions"></a>テンプレート関数の使用

サブスクリプション レベルのデプロイでは、テンプレート関数を使用する場合に、次のようないくつかの重要な考慮事項があります。

* [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [resourceId()](resource-group-template-functions-resource.md#resourceid) 関数は、サポートされています。 これを使用して、サブスクリプション レベルのデプロイで使用されているリソースのリソース ID を取得します。 たとえば、`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))` で、ポリシー定義のリソース ID を取得します。
* [reference()](resource-group-template-functions-resource.md#reference) および [list()](resource-group-template-functions-resource.md#list) 関数がサポートされています。

## <a name="assign-policy"></a>ポリシーの割り当て

次の例は、既存のポリシー定義をサブスクリプションに割り当てます。 ポリシーがパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシーがパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Azure サブスクリプションに組み込みのポリシーを適用するには、次の Azure CLI コマンドを使用します。 この例では、ポリシーにパラメーターがありません。

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Azure サブスクリプションに組み込みのポリシーを適用するには、次の Azure CLI コマンドを使用します。 この例では、ポリシーにパラメーターがあります。

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>ポリシーを定義して割り当てる

ポリシーは同じテンプレートで[定義](../azure-policy/policy-definition.md)して割り当てることができます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
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
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

サブスクリプションでポリシー定義を作成し、サブスクリプションに割り当てるには、次の CLI コマンドを使用します。

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>ロールを割り当てる

次の例では、ユーザーまたはグループにロールを割り当てます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Active Directory グループをサブスクリプションのロールに割り当てるには、次の Azure CLI コマンドを使用します。

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>次の手順
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* リソース グループを作成するには、「[Azure Resource Manager テンプレートでリソース グループを作成する](create-resource-group-in-template.md)」を参照してください。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。

