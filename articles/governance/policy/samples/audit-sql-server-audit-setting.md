---
title: サンプル - SQL Server レベルの監査設定の監査
description: このサンプル ポリシー定義は、SQL Server の監査設定が指定された設定と一致しない場合に、この設定の監査を行います。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 90f91e46db9d25571bc34256ab1db737445ce3e6
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337477"
---
# <a name="sample---audit-sql-server-level-audit-setting"></a>サンプル - SQL Server レベルの監査設定の監査

このポリシーは、SQL Server の監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-server-auditing/azurepolicy.json "Audit SQL Server Level Audit Setting")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

[![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Faudit-sql-server-auditing%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-sql-server-auditing" -DisplayName "Audit SQL Server Level Audit Setting" -description "Audit Audit Setting for SQL Server" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-auditing/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-auditing/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -setting <Audit Setting> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-sql-server-auditing' --display-name 'Audit SQL Server Level Audit Setting' --description 'Audit Audit Setting for SQL Server' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-auditing/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-auditing/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-sql-server-auditing"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

- 他のサンプルについては、「[Azure Policy のサンプル](index.md)」をご確認ください。