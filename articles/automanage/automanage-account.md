---
title: Azure Automanage アカウント
description: Automanage アカウントのしくみと作成方法について説明します。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368529"
---
# <a name="automanage-accounts"></a>Automanage アカウント

Automanage アカウントは、自動操作を実行するために Automanage サービスによって使用される ID です。

Azure portal のエクスペリエンスでは、VM で Automanage が有効になっていると、 **[Enable Azure VM best practice]\(Azure VM のベスト プラクティスを有効にする\)** ブレードの詳細ドロップダウンを使用して、Automanage アカウントを割り当てたり、手動で作成したりすることができます。

Automanage アカウントには、Automanage をオンボードするマシンを含むサブスクリプションに対する **共同作成者** および **リソース ポリシー共同作成者** ロールの両方が付与されます。 複数のサブスクリプションにわたるマシンで同じ Automanage アカウントを使用できます。これにより、その Automanage アカウントに、すべてのサブスクリプションに対する **共同作成者** および **リソース ポリシー共同作成者** アクセス許可が付与されます。

VM が別のサブスクリプションの Log Analytics ワークスペースに接続されている場合は、Automanage アカウントに、その別のサブスクリプションの **共同作成者** と **リソース ポリシー共同作成者** の両方も付与されます。

新しい Automanage アカウントで Automanage を有効にする場合は、サブスクリプションに対する次のアクセス許可が必要です。**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。

既存の Automanage アカウントを使用して Automanage を有効にする場合は、VM を含むリソース グループの **共同作成者** ロールが必要です。

> [!NOTE]
> 自動管理のベスト プラクティスを無効にしても、関連付けられているすべてのサブスクリプションに対する Automanage アカウントのアクセス許可がそのままになります。 サブスクリプションの IAM ページに移動してアクセス許可を手動で削除するか、または Automanage アカウントを削除してください。 Automanage アカウントがまだいずれかのマシンを管理している場合、そのアカウントを削除することはできません。

## <a name="create-an-automanage-account"></a>Automanage アカウントを作成する
Automanage アカウントは、ポータルまたは ARM テンプレートを使用して作成できます。

### <a name="portal"></a>ポータル
1. ポータルの **[Automanage]** ブレードに移動します
1. **[Enable on existing machine]\(既存のマシンで有効にする\)** をクリックします
1. **[詳細設定]** で、[新しいアカウントを作成する] をクリックします
1. 必須フィールドに入力し、 **[作成]** をクリックします

### <a name="arm-template"></a>ARM テンプレート
ARM テンプレートを使用して Automanage アカウントを作成するには、次の 2 つの手順を実行する必要があります。
1. Automanage アカウントを作成する
1. アカウントが操作を実行できるように、十分なアクセス許可を付与する
    1. この手順で作成したアカウントのオブジェクト ID が必要になります。
        1. アカウントのサービス プリンシパルの詳細 (オブジェクト ID を含む) を見つける手順については、[こちら](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)を参照してください。
    1. サービス プリンシパルが見つかったら、**オブジェクト ID** をコピーします。 これは、次のアクセス許可を委任するために必要となるため、保存します。

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Automanage アカウントを作成する (アクセス許可は付与しない)
Automanage アカウントを作成するには、次の ARM テンプレートを `azuredeploy.json` として保存し、次の Azure CLI コマンドを実行します。 完了したら、次の 2 つ目のテンプレートに移動して、アカウントに十分なアクセス許可を委任します。

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Automanage アカウントにアクセス許可を付与する
Automanage アカウントに十分なアクセス許可を付与するには、次の手順を実行する必要があります。
1. 次の ARM テンプレートを `azuredeploy2.json` として保存し、次の Azure CLI コマンドを実行します。
1. プロンプトが表示されたら、作成して保存した Automanage アカウントのオブジェクト ID を入力します。

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ
* [Linux](./automanage-linux.md) および [Windows](./automanage-windows-server.md) 用の Automanage サービスについて説明します。