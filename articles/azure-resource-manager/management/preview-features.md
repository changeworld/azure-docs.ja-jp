---
title: Azure サブスクリプションでプレビュー機能を設定する
description: リソース プロバイダーの Azure サブスクリプションでプレビュー機能の一覧表示、登録、または登録解除を行う方法について説明します。
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429861"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>Azure サブスクリプションでプレビュー機能を設定する

この記事では、Azure サブスクリプションでプレビュー機能を管理する方法について説明します。 プレビュー機能を使用すると、リリース前に新しい機能にオプトインできます。 一部のプレビュー機能は、オプトインを希望するすべてのユーザーが利用できます。 その他のプレビュー機能については、製品チームの承認が必要です。

Azure Feature Exposure Control (AFEC) は、[Microsoft.Features](/rest/api/resources/features) 名前空間を通じて提供されます。 プレビュー機能のリソース ID の形式は次のとおりです。

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>プレビュー機能を一覧表示する

Azure サブスクリプションのすべてのプレビュー機能とその登録状態を一覧表示できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルでは、機能を所有するサービスがプレビュー機能の管理エクスペリエンスに明示的にオプトインしている場合にのみ、プレビュー機能が表示されます。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 検索ボックスに「_サブスクリプション_」と入力し、 **[サブスクリプション]** を選択します。

    :::image type="content" source="./media/preview-features/search.png" alt-text="Azure portal での検索。":::

1. サブスクリプションの名前のリンクを選択します。

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="Azure サブスクリプションの選択。":::

1. 左側のメニューの **[設定]** で、 **[プレビュー機能]** を選択します。

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Azure の [プレビュー機能] メニュー。":::

1. 使用可能なプレビュー機能と現在の登録状態の一覧が表示されます。

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="Azure portal のプレビュー機能の一覧。":::

1. **[プレビュー機能]** では、**名前**、**状態**、または **種類** で一覧をフィルター処理できます。

    - **名前でフィルター**: **表示名** ではなく、プレビュー機能の名前のテキストを含める必要があります。
    - **状態**: ドロップダウン メニューを選択し、状態を選択します。 ポータルでは、"**未登録**" でフィルター処理することはできません。
    - **種類**: ドロップダウン メニューを選択し、種類を選択します。

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Azure portal でのプレビュー機能のフィルター処理。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

サブスクリプションのすべてのプレビュー機能を一覧表示するには、[az feature list](/cli/azure/feature#az_feature_list) コマンドを使用します。

Azure CLI の既定の出力は JSON です。 その他の出力形式の詳細については、[Azure CLI コマンドの出力形式](/cli/azure/format-output-azure-cli)に関するページを参照してください。

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

特定のリソース プロバイダーの出力をフィルター処理するには、`namespace` パラメーターを使用します。 この例では、`output` パラメーターによってテーブル形式が指定されています。

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

特定のプレビュー機能の出力をフィルター処理するには、[az feature show](/cli/azure/feature#az_feature_show) コマンドを使用します。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

サブスクリプションのすべてのプレビュー機能を一覧表示するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドレットを使用します。

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

特定のリソース プロバイダーの出力をフィルター処理するには、`ProviderNamespace` パラメーターを使用します。 既定の出力では、登録済みの機能のみが表示されます。 リソース プロバイダーのすべてのプレビュー機能を表示するには、`ListAvailable` パラメーターと共に `ProviderNamespace` パラメーターを使用します。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

`FeatureName` パラメーターを使用して、特定のプレビュー機能の出力をフィルター処理できます。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>プレビュー機能を登録する

リソース プロバイダーの機能をさらに公開するには、Azure サブスクリプションにプレビュー機能を登録します。 一部のプレビュー機能は承認を必要とします。

サブスクリプションにプレビュー機能が登録されると、"**登録済み**" または "**保留中**" の 2 つの状態のどちらかが表示されます。

- 承認を必要としないプレビュー機能については、状態が "**登録済み**" になります。
- プレビュー機能が承認を必要とする場合、登録状態は "**保留中**" になります。
  - 承認を要求するには、[Azure サポート リクエスト](../../azure-portal/supportability/how-to-create-azure-support-request.md)を送信します。
  - 登録が承認されると、プレビュー機能の状態が "**登録済み**" に変わります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 検索ボックスに「_サブスクリプション_」と入力し、 **[サブスクリプション]** を選択します。
1. サブスクリプションの名前のリンクを選択します。
1. 左側のメニューの **[設定]** で、 **[プレビュー機能]** を選択します。
1. 登録するプレビュー機能のリンクを選択します。
1. **[登録]** を選択します。

    :::image type="content" source="./media/preview-features/register.png" alt-text="Azure portal でのプレビュー機能の登録。":::

1. **[OK]** を選択します。

**[プレビュー機能]** 画面が更新され、プレビュー機能の **[状態]** が表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

プレビュー機能を登録するには、[az feature register](/cli/azure/feature#az_feature_register) コマンドを使用します。

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

登録の状態を表示するには、`az feature show` コマンドを使用します。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> register コマンドを実行すると、機能の登録後に、`az provider register --namespace <provider-name>` を実行して変更を反映するよう求めるメッセージが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

プレビュー機能を登録するには、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドレットを使用します。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

登録の状態を表示するには、`Get-AzProviderFeature` コマンドレットを使用します。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>プレビュー機能の登録を解除する

プレビュー機能の使用が完了したら、Azure サブスクリプションから登録を解除します。 機能の登録を解除すると、2 つの異なる状態のどちらかになります。 ポータルを使用して登録を解除した場合、状態は "**未登録**" に設定されます。 Azure CLI、PowerShell、または REST API を使用して登録を解除した場合、状態は "**登録解除**" に設定されます。 ポータルでは機能の登録が削除されますが、コマンドでは機能の登録が解除されるため、状態が異なります。 どちらの場合も、この機能はサブスクリプションで使用できなくなります。 どちらの場合も、再登録することで、この機能に再度オプトインすることができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

**[プレビュー機能]** でプレビュー機能の登録を解除できます。 **[状態]** が "**未登録**" に変わります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 検索ボックスに「_サブスクリプション_」と入力し、 **[サブスクリプション]** を選択します。
1. サブスクリプションの名前のリンクを選択します。
1. 左側のメニューの **[設定]** で、 **[プレビュー機能]** を選択します。
1. 登録を解除するプレビュー機能のリンクを選択します。
1. **[登録解除]** を選択します。

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Azure portal でのプレビュー機能の登録解除。":::

1. **[OK]** を選択します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

プレビュー機能の登録を解除するには、[az feature unregister](/cli/azure/feature#az_feature_unregister) コマンドを使用します。 `RegistrationState` 状態が "**登録解除**" に変わります。

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

登録解除の状態を表示するには、`az feature show` コマンドを使用します。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> unregister コマンドを実行すると、機能の登録解除後に、`az provider register --namespace <provider-name>` を実行して変更を反映するよう求めるメッセージが表示されます。

"**登録解除**" されたプレビュー機能を検索するには、次のコマンドを使用します。 `<ResourceProvider.Name>` をプロバイダー名 (`Microsoft.Compute` など) に置き換えます。

次の例では、`Microsoft.Compute` リソース プロバイダーの "**登録解除**" されたプレビュー機能を表示します。

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

プレビュー機能の登録を解除するには、[Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature) コマンドレットを使用します。 `RegistrationState` 状態が "**登録解除**" に変わります。

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

登録解除の状態を表示するには、`Get-AzProviderFeature` コマンドレットを使用します。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

次の例では、`Microsoft.Compute` リソース プロバイダーの "**登録解除**" されたプレビュー機能を表示します。

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>次の手順

- REST API 呼び出しを使用し、プレビュー機能の一覧表示、登録、または登録解除を行うには、「[特徴](/rest/api/resources/features)」のドキュメントを参照してください。
- リソース プロバイダーの登録方法の詳細については、「[Azure リソース プロバイダーと種類](resource-providers-and-types.md)」を参照してください。
- リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](azure-services-resource-providers.md)」 (Azure サービスのリソースプロバイダー) を参照してください。
