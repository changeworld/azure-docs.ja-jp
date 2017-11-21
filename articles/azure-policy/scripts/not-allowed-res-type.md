---
title: "Azure Policy の JSON サンプル - 許可されていないリソースの種類 | Microsoft Docs"
description: "この JSON サンプル ポリシーは、指定されたリソースの種類のデプロイを禁止します。"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 8ec252f4c8429df90756f5545483046b7008d525
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="not-allowed-resource-types"></a>許可されないリソースの種類

このポリシーは、指定されたリソースの種類のデプロイを禁止します。 ブロックするリソースの種類の配列を指定します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../policy-templates/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.json "Not allowed resource types")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

[![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fnot-allowed-resourcetypes%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "not-allowed-resourcetypes" -DisplayName "Not allowed resource types" -description "This policy enables you to specify the resource types that your organization cannot deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfResourceTypesNotAllowed <Not allowed resource types> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'not-allowed-resourcetypes' --display-name 'Not allowed resource types' --description 'This policy enables you to specify the resource types that your organization cannot deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "not-allowed-resourcetypes"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

- その他の Azure Policy テンプレートのサンプルについては、「[Templates for Azure Policy (Azure Policy のテンプレート)](../json-samples.md)」を参照してください。
