---
title: 管理グループにリソースをデプロイする
description: Azure Resource Manager テンプレートを使用して、管理グループのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 3b2eeaf2c63a50cda1a32fee94c1e5b99822075d
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228103"
---
# <a name="create-resources-at-the-management-group-level"></a>管理グループ レベルでリソースを作成する

通常、Azure リソースは Azure サブスクリプションのリソース グループにデプロイします。 ただし、管理グループ レベルでリソースを作成することもできます。 管理グループ レベルのデプロイを使用して、[ロールベースのアクセス制御](../../role-based-access-control/overview.md)の割り当てや[ポリシー](../../governance/policy/overview.md)の適用など、そのレベルで意味のあるアクションを実行します。

## <a name="supported-resources"></a>サポートされているリソース

管理グループ レベルでは、次のリソースの種類をデプロイできます。

* [deployments](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>スキーマ

管理グループのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

パラメーター ファイルの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>デプロイ コマンド

管理グループのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドとは異なります。

Azure PowerShell では、[New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment) を使用します。 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

REST API の場合は、[管理グループ スコープでの作成によるデプロイ](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)に関するページを参照してください。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

管理グループ レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="use-template-functions"></a>テンプレート関数を使用する

管理グループ レベルのデプロイの場合、テンプレート関数の使用時にいくつかの重要な考慮事項があります。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [subscription()](template-functions-resource.md#subscription) 関数は、サポートされて**いません**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) 関数がサポートされています。
* [resourceId()](template-functions-resource.md#resourceid) 関数は、サポートされています。 管理グループ レベルのデプロイで使用されているリソースのリソース ID を取得するには、これを使用します。 リソース グループ パラメーターに値を指定しないでください。

  たとえば、ポリシー定義のリソース ID を取得するには、次を使用します。
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  返されるリソース ID の形式は次のとおりです。
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>ポリシーの作成

### <a name="define-policy"></a>ポリシーの定義

次の例は、管理グループ レベルでポリシーを[定義する](../../governance/policy/concepts/definition-structure.md)方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
    }
  ]
}
```

### <a name="assign-policy"></a>ポリシーの割り当て

次の例では、既存のポリシー定義を管理グループに割り当てています。 ポリシーがパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシーがパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>テンプレートのサンプル

* [リソース グループ、ポリシー、およびポリシーの割り当てを作成します](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>次のステップ

* ロールの割り当てについては、「[RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する](../../role-based-access-control/role-assignments-template.md)」をご覧ください。
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](template-syntax.md)に関するページを参照してください。
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](template-functions.md)に関するページを参照してください。