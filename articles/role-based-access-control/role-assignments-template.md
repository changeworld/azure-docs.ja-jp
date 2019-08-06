---
title: RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure Resource Manager テンプレートを使用してユーザー、グループ、アプリケーションの Azure リソースへのアクセスを管理する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360466"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 Azure PowerShell または Azure CLI を使う以外に、RBAC と [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使って Azure リソースへのアクセスを管理することもできます。 リソースを一貫して繰り返しデプロイする場合は、テンプレートが便利です。 この記事では、RBAC とテンプレートを使ってアクセスを管理する方法について説明します。

## <a name="assign-role-to-resource-group-or-subscription"></a>リソース グループまたはサブスクリプションにロールを割り当てる

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。 以下のテンプレートでは次のことを示します。
- リソース グループまたはサブスクリプションのどちらかのスコープでユーザー、グループ、またはアプリケーションにロールを割り当てる方法
- 所有者、共同作成者、閲覧者のロールをパラメーターとして指定する方法

テンプレートを使うには、次の入力を指定する必要があります。
- ロールを割り当てるユーザー、グループ、またはアプリケーションの一意識別子
- 割り当てるロール
- ロール割り当てに使用する一意の識別子。または既定の識別子を使用できます

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

以下では、テンプレートをデプロイした後でユーザーにリソース グループの閲覧者ロールを割り当てる例を示します。

![テンプレートを使用したロールの割り当て](./media/role-assignments-template/role-assignment-template.png)

ロール割り当てのスコープは、デプロイのレベルから特定できます。 この記事では、リソース グループとサブスクリプション レベルのデプロイ コマンドの両方を示します。

## <a name="assign-role-to-resource"></a>リソースにロールを割り当てる

リソース レベルでロールの割り当てを作成する必要がある場合は、ロールの割り当ての形式が異なります。 ロールを割り当てるリソースのリソース プロバイダーの名前空間とリソースの種類を指定します。 ロールの割り当ての名前にリソースの名前も含めます。

ロールの割り当ての種類と名前には、次の形式を使用します。

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

次のテンプレートでは、ストレージ アカウントをデプロイし、ロールを割り当てます。 リソース グループ コマンドでデプロイします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[parameters('location')]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
      "dependsOn": [
          "[variables('storageName')]"
      ],
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

以下では、テンプレートをデプロイした後でユーザーにストレージ アカウントの共同作成者ロールを割り当てる例を示します。

![テンプレートを使用したロールの割り当て](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Azure PowerShell を使用してテンプレートを展開する

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Azure PowerShell を使用して、リソース グループまたはサブスクリプションのどちらかに以前のテンプレートをデプロイするには、次の手順に従います。

1. rbac-rg.json という名前の新しいファイルを作成し、前に示したテンプレートをコピーします。

1. [Azure PowerShell](/powershell/azure/authenticate-azureps) にサインインします。

1. ユーザー、グループ、またはアプリケーションの一意識別子を取得します。 たとえば、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) コマンドを使って Azure AD ユーザーの一覧を表示できます。

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. このテンプレートでは、ロールの割り当てを識別するために使用される GUID の既定値を生成します。 特定の GUID を指定する必要がある場合は、roleNameGuid パラメーターでその値を渡します。 この識別子の形式は `11111111-1111-1111-1111-111111111111` になります。

リソースまたはリソース グループのレベルでロールを割り当てるには、次の手順に従います。

1. リソース グループの例を作成します。

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドを使って、デプロイを開始します。

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    出力の例を次に示します。

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

サブスクリプションのレベルでロールを割り当てるには、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) コマンドを使用して、デプロイの場所を指定します。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

リソース グループのデプロイ コマンドへも同様に出力します。

## <a name="deploy-template-using-the-azure-cli"></a>Azure CLI を使用してテンプレートを展開する

リソース グループまたはサブスクリプションのどちらかに Azure CLI を使用して以前のテンプレートをデプロイするには、次の手順に従います。

1. rbac-rg.json という名前の新しいファイルを作成し、前に示したテンプレートをコピーします。

1. [Azure CLI](/cli/azure/authenticate-azure-cli) にサインインします。

1. ユーザー、グループ、またはアプリケーションの一意識別子を取得します。 たとえば、[az ad user show](/cli/azure/ad/user#az-ad-user-show) コマンドを使用して Azure AD ユーザーを表示できます。

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. このテンプレートでは、ロールの割り当てを識別するために使用される GUID の既定値を生成します。 特定の GUID を指定する必要がある場合は、roleNameGuid パラメーターでその値を渡します。 この識別子の形式は `11111111-1111-1111-1111-111111111111` になります。

リソースまたはリソース グループのレベルでロールを割り当てるには、次の手順に従います。

1. リソース グループの例を作成します。

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) コマンドを使って、デプロイを開始します。

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    出力の例を次に示します。

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

サブスクリプションのレベルでロールを割り当てるには、[az deployment create](/cli/azure/deployment#az-deployment-create) コマンドを使用して、デプロイの場所を指定します。

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

リソース グループのデプロイ コマンドへも同様に出力します。

## <a name="next-steps"></a>次の手順

- [クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?term=rbac)
