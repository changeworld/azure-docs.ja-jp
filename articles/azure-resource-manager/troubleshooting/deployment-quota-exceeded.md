---
title: デプロイ クォータの超過
description: リソース グループ履歴でデプロイが 800 を超えたときのエラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 64845ad3aed7dcccb7623a84552459ac9de78945
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489853"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>デプロイ数が 800 を超えたときのエラーを解決する

各リソース グループには、そのデプロイ履歴が 800 までという上限があります。 この記事では、許可されている 800 を超え、デプロイに失敗したときに表示されるエラーについて説明します。 このエラーを解決するには、リソース グループ履歴でデプロイ履歴からデプロイを削除します。 履歴からデプロイを削除しても、デプロイされたリソースには影響が出ません。

Azure Resource Manager では、上限に近づくと履歴からデプロイが自動的に削除されます。 このエラーは、次のいずれかの理由で表示される場合があります。

1. リソース グループに、デプロイ履歴からの削除を防ぐ [CanNotDelete](../management/lock-resources.md) ロックが設定されている。
1. 自動削除をオプト アウトした。
1. 多数のデプロイが同時に実行され、自動削除の速度が不十分なため合計数を減らすことができない。

ロックを解除する方法、または自動削除をオプトインする方法については、「[デプロイ履歴からの自動削除](../templates/deployment-history-deletions.md)」を参照してください。

この記事では、デプロイを履歴から手動で削除する方法について説明します。

## <a name="symptom"></a>症状

デプロイ中、現在のデプロイで 800 デプロイのクォータを超過することを示すエラーが表示されます。

## <a name="solution"></a>解決策

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az group deployment delete](/cli/azure/deployment/group#az_deployment_group_delete) コマンドを使用し、履歴からデプロイを削除します。

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

5 日を経過したデプロイをすべて削除するには、次を使用します。

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

次のコマンドでは、デプロイ履歴の現在のデプロイ数を取得できます。

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) コマンドを使用し、履歴からデプロイを削除します。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5 日を経過したデプロイをすべて削除するには、次を使用します。

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object -Property Timestamp -LT -Value ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

次のコマンドでは、デプロイ履歴の現在のデプロイ数を取得できます。

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

---
