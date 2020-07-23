---
title: デプロイ履歴の削除
description: Azure Resource Manager でデプロイ履歴からデプロイを自動削除するしくみについて説明します。 履歴が上限の 800 を超えそうになるとデプロイが削除されます。
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 8ec3291dc5e35689d4e2c614949e0328057fbfd3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248985"
---
# <a name="automatic-deletions-from-deployment-history"></a>デプロイ履歴からの自動削除

テンプレートをデプロイするたびに、デプロイに関する情報がデプロイ履歴に書き込まれます。 各リソース グループには、そのデプロイ履歴が 800 までという上限があります。

Azure Resource Manager では、上限に近づくとすぐに履歴からデプロイの自動削除が開始されます。 自動削除という動作は過去になかったものです。 以前は、エラーを避ける目的で、デプロイ履歴から手動でデプロイを削除する必要がありました。 **この機能はまだ Azure に追加されていません。ユーザーがオプトアウトを希望する場合に備えて、今後の変更についてお知らせしていきます。**

> [!NOTE]
> 履歴からデプロイを削除しても、デプロイされたリソースには影響が出ません。
>
> リソース グループに [CanNotDelete ロック](../management/lock-resources.md) が設定されている場合、そのリソース グループのデプロイを削除することはできません。 デプロイ履歴の自動削除を利用するには、このロックを削除する必要があります。

## <a name="when-deployments-are-deleted"></a>デプロイが削除されるタイミング

デプロイが 775 件に達すると、ご自分の履歴からデプロイが削除されます。 Azure Resource Manager では、履歴が 750 に減るまでデプロイを削除します。 常に、最も古いデプロイが先に削除されます。

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="デプロイ履歴からの削除":::

> [!NOTE]
> 開始番号 (775) と終了番号 (750) は変わる場合もあります。
>
> リソース グループが既に 800 の上限に達している場合、次回のデプロイはエラーで失敗します。 自動削除プロセスがすぐに開始されます。 少し待つと、 デプロイを再試行できます。

デプロイに加え、[what-if 操作](template-deploy-what-if.md)の実行時またはデプロイの検証時にも削除を始動させることができます。

履歴に含まれるものと同じ名前をデプロイに付けると、履歴のその場所をリセットすることになります。 そのデプロイは履歴の中で最も新しい場所に移動します。 エラー後、[そのデプロイまでロールバック](rollback-on-error.md)した場合もデプロイの場所がリセットされます。

## <a name="opt-out-of-automatic-deletions"></a>自動削除のオプトアウト

履歴の自動削除をオプトアウトできます。 **このオプションは、デプロイ履歴を自分で管理する場合にのみ使用してください。** 履歴の 800 デプロイという上限は依然、適用されます。 800 デプロイを超えると、エラーが表示され、デプロイに失敗します。

自動削除を無効にするには、`Microsoft.Resources/DisableDeploymentGrooming` 機能フラグを登録します。 機能フラグを登録すると、Azure サブスクリプション全体の自動削除がオプトアウトされます。 特定のリソース グループだけをオプトアウトすることはできません。 自動削除を再び有効にするには、この機能フラグの登録を解除します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell の場合、[Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature) を使用します。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

サブスクリプションの現状を確認するには、次を使用します。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

自動削除を再び有効にするには、Azure REST API または Azure CLI を使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合、[az feature register](/cli/azure/feature#az-feature-register) を使用します。

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

サブスクリプションの現状を確認するには、次を使用します。

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

自動削除を再び有効にするには、[az feature unregister](/cli/azure/feature#az-feature-unregister) を使用します。

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

REST API の場合、[Features - Register](/rest/api/resources/features/register) を使用します。

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

サブスクリプションの現状を確認するには、次を使用します。

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

自動削除を再び有効にするには、[機能 - 登録解除](/rest/api/resources/features/unregister)を使用します。

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>次のステップ

* デプロイ履歴を表示する方法については、「[Azure Resource Manager でのデプロイ履歴の表示](deployment-history.md)」を参照してください。
