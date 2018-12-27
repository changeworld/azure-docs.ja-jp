---
title: サンプル - 診断設定の監査
description: このサンプル ポリシーでは、指定されたリソースの種類で診断設定が有効になっていない場合に監査を行います。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 283db04ba0edc988e9156d6b6681c9d8024127ce
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318828"
---
# <a name="audit-diagnostic-setting"></a>診断設定の監査

このビルトイン ポリシーでは、指定されたリソースの種類で診断設定が有効になっていない場合に監査を行います。 リソースの種類の配列を指定して、診断設定が有効になっているかどうかを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../../policy-templates/samples/Monitoring/audit-diagnostic-setting/azurepolicy.json "Audit diagnostic setting")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。 ビルトイン ポリシーを取得するには、ID `7f89b1eb-583c-429a-8828-af049802c1d9` を使用します。

## <a name="parameters"></a>parameters

パラメーター値を渡すには、次の形式を使用してください。

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

ポリシーを割り当てるときに、利用可能なビルトイン定義から **[診断設定の監査]** を選択します。

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzureRmPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- 他のサンプルについては、「[Azure Policy のサンプル](index.md)」をご確認ください。