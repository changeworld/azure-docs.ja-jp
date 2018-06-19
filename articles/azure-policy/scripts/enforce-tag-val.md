---
title: Azure Policy の JSON サンプル - タグとその値を強制 | Microsoft Docs
description: この JSON サンプル ポリシーは、指定されたタグ名と値が要件です。
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 08702d9886a0d6282089bb8d73715be83e1a9a6a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601124"
---
# <a name="enforce-tag-and-its-value"></a>タグとその値を強制

このポリシーは、指定されたタグ名と値が要件です。 強制的に適用するタグ名と値を指定します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../policy-templates/samples/built-in-policy/enforce-tag-value/azurepolicy.json "Enforce tag and its value")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

[![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fenforce-tag-value%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "enforce-tag-value" -DisplayName "Enforce tag and its value" -description "Enforces a required tag and its value." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/enforce-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/enforce-tag-value/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -tagName <tagName> -tagValue <tagValue> -PolicyDefinition $definition
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
az policy definition create --name 'enforce-tag-value' --display-name 'Enforce tag and its value' --description 'Enforces a required tag and its value.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/enforce-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/enforce-tag-value/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-tag-value"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

- 他の例については、「[Azure Policy のサンプル](../json-samples.md)」をご確認ください。