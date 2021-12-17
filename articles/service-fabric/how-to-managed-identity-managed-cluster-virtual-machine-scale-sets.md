---
title: Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する
description: この記事では、Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する方法について説明します
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 75f421ef750907a172ac3cf5c846b6b35f448521
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544927"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 マネージド クラスターのノード タイプでマネージド ID を使用できるようにするために、プロパティ `vmManagedIdentity` が、使用される ID の一覧 `userAssignedIdentities` を含むノード タイプ定義に追加されました。 機能には、マネージド ID を非マネージド クラスター内でどのように使用できるか (たとえば、[Azure Key Vault 仮想マシン スケール セット拡張機能](../virtual-machines/extensions/key-vault-windows.md)でマネージド ID を使用するなど) が反映されます。

ノード タイプのマネージド ID を利用する Service Fabric マネージド クラスターのデプロイの例については、[こちらのテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)を参照してください。 例には 2 つのテンプレートが含まれています。

1. **マネージド ID とロールの割り当て**: マネージド ID とロールの割り当てを作成して、Service Fabric RP でマネージド クラスターの仮想マシン スケール セットに ID を割り当てることができるようにするテンプレート。 これは、ノード タイプ リソースでマネージド ID を使用する前に 1 回だけデプロイする必要があります。

2. **マネージド クラスターとノード タイプ**: 前に作成したマネージド ID を使用する Service Fabric マネージド クラスターとノード タイプ リソース用のテンプレート。

> [!NOTE]
> 現在、この機能でサポートされているのはユーザー割り当て ID みです。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* PowerShell の使用を計画している場合は、CLI リファレンス コマンドを実行するために Azure CLI を[インストール](/cli/azure/install-azure-cli)します。

## <a name="1-create-identity-and-role-assignment"></a>1. ID とロールの割り当てを作成する

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てのマネージド ID は、デプロイ時に作成するために、Azure Resource Manager (ARM) テンプレートのリソース セクションで定義できます。

```JSON
{
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
  "name": "[parameters('userAssignedIdentityName')]",
  "apiVersion": "2018-11-30",
  "location": "[resourceGroup().location]"
}
```

または PowerShell を使用して作成されます。

```powershell
New-AzResourceGroup -Name <managedIdentityRGName> -Location <location>
New-AzUserAssignedIdentity -ResourceGroupName <managedIdentityRGName> -Name <userAssignedIdentityName>
```

### <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Service Fabric リソース プロバイダーでロールの割り当てを追加する

Service Fabric リソース プロバイダー アプリケーションを使用して、マネージド ID にロールの割り当てを追加します。 この割り当てにより、Service Fabric リソース プロバイダーは、前の手順で作成したマネージド クラスターの仮想マシン スケール セットに ID を割り当てることができます。 これは 1 回限りの手順です。

Service Fabric リソース プロバイダー アプリケーションのサービス プリンシパルを取得します。

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> 正しいサブスクリプションを使用していることを確認します。サブスクリプションが別のテナントにある場合は、プリンシパル ID が変わります。

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
```

前の出力の **ID** を **principalId** として使用し、下のロール定義 ID を **roleDefinitionId** として使用します (テンプレートまたは PowerShell コマンドで該当する場合)。

|ロール定義名|ロール定義 ID|
|----|-------------------------------------|
|Managed Identity Operator|f1a07417-d97a-45cb-824c-7a7467783830|


このロールの割り当てを、プリンシパル ID とロール定義 ID を使用するリソース セクション テンプレートに定義できます。

```json
{
  "type": "Microsoft.Authorization/roleAssignments",
  "apiVersion": "2020-04-01-preview",
  "name": "[parameters('vmIdentityRoleNameGuid')]",
  "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
  "dependsOn": [
    "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "properties": {
    "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
    "principalId": "00000000-0000-0000-0000-000000000000"
  }
}
```
> [!NOTE]
> vmIdentityRoleNameGuid は、有効な GUID である必要があります。 このロールの割り当てを含む同じテンプレートを再びデプロイする場合は、GUID が最初に使用したものと同じであることを確認します。また、このリソースを 1 回だけ作成する必要がある場合は削除します。

または、PowerShell でプリンシパル ID とロール定義名を使用して作成できます。

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

### <a name="deploy-managed-identity-and-role-assignment"></a>マネージド ID とロールの割り当てをデプロイする
New-AzResourceGroupDeployment コマンドレットを実行してマネージド ID を作成し、ロールの割り当てを追加します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <managedIdentityRGName> -TemplateFile ".\MangedIdentityAndSfrpRoleAssignment.json" -TemplateParameterFile ".\MangedIdentityAndSfrpRoleAssignment.Parameters.json" -Verbose
```

## <a name="2-assign-identity-to-the-node-type-resource"></a>2. ノード タイプ リソースに ID を割り当てる

### <a name="add-managed-identity-properties-to-node-type-definition"></a>マネージド ID プロパティをノード タイプ定義に追加する

最後に、最初の手順で作成した ID の完全なリソース ID を使用して、マネージド クラスターのノード タイプ定義に `vmManagedIdentity` プロパティと `userAssignedIdentities` プロパティを追加します。 `apiVersion` には、必ず **2021-05-01** 以降を使用してください。

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "apiVersion": "2021-05-01",
  "properties": {
    "isPrimary": true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2_v2",
    "vmImagePublisher": "MicrosoftWindowsServer",
    "vmImageOffer": "WindowsServer",
    "vmImageSku": "2019-Datacenter",
    "vmImageVersion": "latest",
    "vmManagedIdentity": {
      "userAssignedIdentities": [
        "[parameters('userAssignedIdentityResourceId')]"
      ]
    }
  }
}
```

### <a name="deploy-the-node-type-resource-assigning-the-identity"></a>ID を割り当てるノード タイプ リソースをデプロイする

New-AzResourceGroupDeployment コマンドレットを実行して、マネージド ID をノード タイプ リソースに割り当てる Service Fabric マネージド クラスター テンプレートをデプロイします。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <sfmcRGName> -TemplateFile ".\SfmcVmMangedIdentity.json" -TemplateParameterFile ".\SfmcVmMangedIdentity.Parameters.json" -Verbose
```

デプロイ後、作成されたマネージド ID は指定されたノード タイプの仮想マシン スケール セットに追加されており、非マネージド クラスターと同様に、想定どおりに使用できます。

## <a name="troubleshooting"></a>トラブルシューティング

ロールの割り当てを適切に追加できなかった場合は、デプロイ時に次のエラーが発生します。

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure portal deployment error showing the client with SFRP's object/application ID not having permission to perform identity management activity (SFRP のオブジェクトまたはアプリケーション ID を持つクライアントに、ID 管理アクティビティを実行する権限がないことを示す Azure portal のデプロイ エラー)":::

この場合は、ロールの割り当てが "マネージド ID オペレーター" ロールで正常に作成されていることを確認してください。 ロールの割り当ては、次に示すように、Azure portal のマネージド ID リソースの [アクセス制御] で確認できます。

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-portal.png" alt-text="Azure portal に表示されるユーザー割り当てマネージド ID に対する Service Fabric リソース プロバイダーのロール割り当てプロパティ":::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターにアプリをデプロイする](./tutorial-managed-cluster-deploy-app.md)
