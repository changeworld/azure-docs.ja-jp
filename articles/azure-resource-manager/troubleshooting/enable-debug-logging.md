---
title: デバッグ ログの有効化
description: デバッグ ログを有効にして、Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルでデプロイされた Azure リソースのトラブルシューティングを行う方法について説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcd2cbdf052f934bb797b3f1dab148d951d09167
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989651"
---
# <a name="enable-debug-logging"></a>デバッグ ログの有効化

デプロイ エラーのトラブルシューティングを行う場合は、より多くの情報を収集するのに役立ちます。 デバッグ ログを有効にするには、Azure PowerShell を使用します。 デプロイの要求と応答に関するデータを取得して、問題の原因を確認できます。 デバッグ ログは、Azure Resource Manager (ARM テンプレート) ファイルと Bicep ファイルで動作します。

## <a name="get-debug-information"></a>デバッグ情報を取得する

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して `DeploymentDebugLogLevel` パラメーターを `All`、`ResponseContent`、`RequestContent` のいずれかに設定します。 デバッグ ログを有効にすると、[Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) などのコマンドによって、シークレット (パスワード、`listKeys` など) がログされる可能性があるという警告が表示されます。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

出力には、デバッグ ログが表示されます。

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

デプロイ操作のすべてのプロパティを表示するには:

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

`StatusMessage` や `StatusCode` などのプロパティを指定して、出力をフィルター処理できます。

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してデバッグ ログ記録を有効にすることはできませんが、デバッグ ログ データを取得できます。

[az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) コマンドを使用して、デプロイ操作を取得します。

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

次のコマンドを使用して、要求の内容を取得します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

次のコマンドを使用して、応答の内容を取得します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>入れ子になったテンプレート

[入れ子になった](../templates/linked-templates.md#nested-template) ARM テンプレートのデバッグ情報をログに記録するには、[Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) `debugSetting` 要素を使用します。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep では、`Microsoft.Resources/deployments` でなく[モジュール](../bicep/modules.md)が使用されます。 modules を使用すると、コードを再利用して別の Bicep ファイルから Bicep ファイルをデプロイできます。

## <a name="next-steps"></a>次の手順

- [一般的なデプロイのエラー](common-deployment-errors.md)
- [エラー コードの検索](find-error-code.md)
- [トラブルシューティング テンプレートを作成する](create-troubleshooting-template.md)
