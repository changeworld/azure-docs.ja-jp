---
title: エラー コードを見つける
description: Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してデプロイされた Azure リソースをトラブルシューティングするためのエラー コードの検出方法について説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dc33dec3f043332b4ce5b2e7fe53b9f16d41d54f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846313"
---
# <a name="find-error-codes"></a>エラー コードを見つける

Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用して Azure リソースのデプロイが失敗すると、エラー コードを受け取ります。 この記事では、問題をトラブルシューティングするためにエラー コードを見つける方法について説明します。 エラー コードの詳細については、[一般的なデプロイ エラー](common-deployment-errors.md)に関する記事を参照してください。

## <a name="error-types"></a>エラーの種類

デプロイに関するエラーには 3 種類あります。

- **検証エラー** デプロイ開始前に、ファイル中の構文エラーによって発生します。 これらのエラーはエディターで特定できます。
- **プレフライト検証エラー** は、デプロイ コマンドが実行されてもリソースがデプロイされない場合に発生します。 これらのエラーは、デプロイを開始しなくても検出されます。 たとえば、パラメーターの値が正しくない場合は、プレフライト検証でエラーが検出されます。
- **デプロイ エラー** デプロイのプロセスで発生し、デプロイの進行状況を確認することでのみ検出できます。

どの種類のエラーでも、デプロイのトラブルシューティングに使用できるエラー コードが出力されます。 検証とプレフライトのエラーはアクティビティ ログに表示されますが、デプロイ履歴には表示されません。 構文エラーがある Bicep ファイルは JSON にコンパイルされず、アクティビティ ログに表示されません。

構文エラーを識別するには、[Visual Studio Code](https://code.visualstudio.com) と最新の [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)または [Azure Resource Manager ツールの拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)を使用できます。

## <a name="validation-errors"></a>検証エラー

テンプレートはデプロイ プロセス中に検証され、エラー コードが表示されます。 デプロイを実行する前に、Azure PowerShell または Azure CLI を使用して検証テストを実行し、検証とプレフライト エラーを識別できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ARM テンプレートは、ポータルからデプロイできます。 テンプレートに構文エラーがある場合は、デプロイを実行しようとすると検証エラーが表示されます。 ポータル デプロイの詳細については、「[カスタム テンプレートからリソースをデプロイする](../templates/deploy-portal.md#deploy-resources-from-custom-template)」を参照してください。

次の例では、ストレージ アカウントのデプロイを試み、検証エラーが発生します。

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Azure portal の検証エラーのスクリーンショット。":::

詳細については、メッセージを選択します。 テンプレートには、エラー コード `InvalidTemplate` を含む構文エラーがあります。 **[概要]** は、式に閉じかっこが不足していることを示しています。

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="構文エラーを示す検証エラー メッセージのスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

デプロイを実行する前に ARM テンプレートを検証するには、[Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment) を実行します。

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

出力には、テンプレートのトラブルシューティングと修正に使用できる `InvalidTemplateDeployment` や `AccountNameInvalid` などのエラー コードが表示されます。

Bicep ファイルの場合、構文検証問題の出力にはパラメーター エラーが表示されます。

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

追加のトラブルシューティング情報を取得するには、Bicep の [build](../bicep/bicep-cli.md) コマンドを使用します。 出力には、かっこで囲まれた各エラーの行と列番号と、エラー メッセージが表示されます。

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

展開テンプレートの検証に使用できる PowerShell コマンドレットは他にもあります。

- [Test-AzDeployment](/powershell/module/az.resources/test-azdeployment) (サブスクリプション レベルのデプロイの場合)。
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

デプロイを実行する前に ARM テンプレートを検証するには、[az deployment group validate](/cli/azure/deployment/group#az_deployment_group_validate) を実行します。

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

出力には、テンプレートのトラブルシューティングと修正に使用できる `InvalidTemplateDeployment` や `AccountNameInvalid` などのエラー コードが表示されます。

Bicep ファイルの場合、出力には、かっこで囲まれた各エラーの行と列番号と、エラー メッセージが表示されます。

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

展開テンプレートの検証に使用できる Azure CLI コマンドは他にもあります。

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>展開エラー

Azure リソースをデプロイするために、いくつかの操作が処理されます。 操作が検証に合格してもデプロイ中に失敗すると、デプロイ エラーが発生します。 各デプロイ操作とリソース グループの各デプロイに関するメッセージを表示できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

デプロイの操作に関するメッセージを表示するには、リソース グループの **アクティビティ ログ** を使用します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. **[リソース グループ]** に移動し、デプロイのリソース グループ名を選択します。
1. **[アクティビティ ログ]** を選択します。
1. フィルターを使用して、操作のエラー ログを検索します。

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="失敗したデプロイが強調表示されている、リソース グループのアクティビティ ログのスクリーンショット。":::

1. エラー ログを選択すると、操作の詳細が表示されます。

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="失敗したデプロイのエラー メッセージを示す、アクティビティ ログの詳細のスクリーンショット。":::

デプロイの結果を表示するには、次のようにします。

1. リソース グループに移動します。
1. **[設定]**  >  **[デプロイ]** を選択します。
1. デプロイの **[エラーの詳細]** を選択します。

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="失敗したデプロイのエラーの詳細へのリソース グループのリンクのスクリーンショット。":::

1. エラー メッセージとエラー コード `NoRegisteredProviderFound` が表示されます。

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="デプロイ エラーの詳細を示すメッセージのスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell でデプロイの操作メッセージを表示するには、[Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) を使用します。

デプロイのすべての操作を表示するには、次のようにします。

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

特定のプロパティの型を指定するには、次のようにします。

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

デプロイの結果を取得するには、[Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment) を使用します。

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でデプロイの操作メッセージを表示するには、[az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) を使用します。

デプロイのすべての操作を表示するには、次のようにします。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

特定のプロパティの型を指定するには、次のようにします。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

デプロイの結果を取得するには、[az deployment group show](/cli/azure/deployment/group#az_deployment_group_show) を使用します。

```azurecli
az deployment group show \
  --resource-group examplegroup \
  --name exampledeployment
```

---

## <a name="next-steps"></a>次の手順

- [一般的なデプロイのエラー](common-deployment-errors.md)
- [デバッグ ログの有効化](enable-debug-logging.md)
- [トラブルシューティング テンプレートを作成する](create-troubleshooting-template.md)
