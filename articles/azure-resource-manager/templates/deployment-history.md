---
title: デプロイ履歴
description: Azure Resource Manager のデプロイ操作を、ポータル、PowerShell、Azure CLI、および REST API を使用して表示する方法について説明します。
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60bc06ab15e7f36a5c67ff5fc4de9da8bad28f88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310289"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager でのデプロイ履歴の表示

Azure Resource Manager では、デプロイ履歴を表示することができます。 過去のデプロイにおける特定の操作を調べ、どのリソースがデプロイされたかを確認できます。 この履歴には、エラーに関する情報が含まれます。

リソース グループのデプロイ履歴は、800 件のデプロイに制限されます。 この制限に近づくと、デプロイは履歴から自動的に削除されます。 詳細については、「[デプロイ履歴からの自動削除](deployment-history-deletions.md)」を参照してください。

特定のデプロイ エラーの解決方法については、「[よくある Azure デプロイ エラーのトラブルシューティングに関する記事](common-deployment-errors.md)」を参照してください。

## <a name="get-deployments-and-correlation-id"></a>デプロイと関連付け ID の取得

デプロイの詳細を表示するには、Azure portal、PowerShell、Azure CLI、または REST API を使用します。 各デプロイには、関連イベントの追跡に使用される関連付け ID があります。 [Azure サポート リクエストを作成した](../../azure-portal/supportability/how-to-create-azure-support-request.md)場合、関連付け ID をサポートから求められることがあります。 サポートは関連付け ID を使用し、デプロイを失敗させた操作を特定します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 調べたいリソース グループを選択します。

1. **[デプロイ]** の下のリンクを選択します。

   :::image type="content" source="media/deployment-history/select-deployment-history.png" alt-text="デプロイの成功を示すリソース グループの概要のスクリーンショット。":::

1. デプロイ履歴からいずれかのデプロイを選択します。

   :::image type="content" source="media/deployment-history/select-details.png" alt-text="リソース デプロイの強調表示されたリンクのスクリーンショット。":::

1. デプロイの概要が表示され、関連付け ID を確認できます。

   :::image type="content" source="media/deployment-history/show-correlation-id.png" alt-text="関連付け ID が強調表示されているデプロイ履歴のスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

リソース グループのすべてのデプロイを一覧表示するには、[Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) コマンドを使用します。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

リソース グループから特定のデプロイを取得するには、`DeploymentName` パラメーターを追加します。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

相関 ID を取得するには、以下を使用します。

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

リソース グループのすべてのデプロイを一覧表示するには、[az deployment group list](/cli/azure/deployment/group#az_deployment_group_list) を使用します。

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

特定のデプロイを取得するには、[az deployment group show](/cli/azure/deployment/group#az_deployment_group_show) を使用します。

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

相関 ID を取得するには、以下を使用します。

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

リソース グループのデプロイを一覧表示するには、次の操作を行います。 要求で使用する最新の API バージョン番号については、「[デプロイ - リソース グループごとの一覧表示](/rest/api/resources/deployments/listbyresourcegroup)」をご覧ください。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

特定のデプロイを取得するには、次の操作を行います。 要求で使用する最新の API バージョン番号については、「[デプロイ - 取得](/rest/api/resources/deployments/get)」を参照してください。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

応答に関連付け ID が含まれています。

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "11111111-1111-1111-1111-111111111111",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>デプロイ操作とエラー メッセージの取得

各デプロイには、複数の操作が含まれる可能性があります。 デプロイの詳細については、デプロイの操作に関する記事をご覧ください。 デプロイが失敗すると、デプロイ操作にエラー メッセージが含まれます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. デプロイの概要で、 **[操作の詳細]** を選択します。

   :::image type="content" source="media/deployment-history/get-operation-details.png" alt-text="操作の詳細のリンクが強調表示されている、失敗したデプロイのスクリーンショット。":::

1. 該当するデプロイ手順の詳細が表示されます。 エラーが発生した場合は、この詳細にエラー メッセージが表示されます。

   :::image type="content" source="media/deployment-history/see-operation-details.png" alt-text="失敗したデプロイの操作の詳細のスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

リソース グループにデプロイするためのデプロイ操作を表示するには、[Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) コマンドを使用します。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

失敗した操作を表示するには、**Failed** 状態で操作をフィルターします。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }
```

失敗した操作のステータス メッセージを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

リソース グループにデプロイするためのデプロイ操作を表示するには、[az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) コマンドを使用します。 Azure CLI 2.6.0 以降である必要があります。

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

失敗した操作を表示するには、**Failed** 状態で操作をフィルターします。

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed']"
```

失敗した操作のステータス メッセージを取得するには、次のコマンドを使用します。

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

デプロイ操作を取得するには、次の操作を行います。 要求で使用する最新の API バージョン番号については、「[デプロイ操作 - 一覧表示](/rest/api/resources/deploymentoperations/list)」を参照してください。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

応答にエラー メッセージが含まれています。

```json
{
  "value": [
    {
      "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeployment/operations/1234567890ABCDEF",
      "operationId": "1234567890ABCDEF",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "11111111-1111-1111-1111-111111111111",
        "serviceRequestId": "11111111-1111-1111-1111-111111111111",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storage",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storage"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>次のステップ

- 特定のデプロイ エラーの解決方法については、「[よくある Azure デプロイ エラーのトラブルシューティングに関する記事](common-deployment-errors.md)」を参照してください。
- 履歴でのデプロイの管理方法の詳細については、「[デプロイ履歴からの自動削除](deployment-history-deletions.md)」を参照してください。
- デプロイ前に、テンプレートによって行われる変更をプレビューするには、「[ARM テンプレートのデプロイの what-if 操作](deploy-what-if.md)」を参照してください。
