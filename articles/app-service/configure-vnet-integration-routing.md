---
title: 仮想ネットワーク統合にアプリケーションのルーティングを構成します。
description: このハウツー記事では、リージョン仮想ネットワーク統合でアプリ ルーティングを構成する手順について説明します。
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: b62a8c4bcef5e5556820f6f4e816f01566d44f72
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892042"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Azure App Service 仮想ネットワーク統合のルーティングを管理する

アプリケーションのルーティングを構成するときは、Azure 仮想ネットワーク (VNet) にすべてのトラフィックまたはプライベート トラフィック ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) トラフィックとも呼ばれます) のみをルーティングできます。 この記事では、アプリケーションのルーティングを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

アプリは、リージョン VNet 統合機能を使用して既に統合されています。

## <a name="configure-in-the-azure-portal"></a>Azure portal で構成する

次の手順に従って、ポータルからアプリの **[ルートすべて]** を無効にします。

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="[ルートすべて] の有効化を示すスクリーンショット。":::

1. アプリ ポータルの **[ネットワーク]**  >  **[VNet 統合]** に移動します。
1. **[ルートすべて]** を **[無効]** に設定します。
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="[ルートすべて] の無効化を示すスクリーンショット。":::

1. **[はい]** を選択して確定します。

## <a name="configure-with-the-azure-cli"></a>Azure CLI を使用して構成する

Azure CLI を使用して **[ルートすべて]** を構成することもできます。 必要な az の最小バージョンは 2.27.0 です。

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>Azure PowerShell を使用して構成する

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク統合を有効にす](./configure-vnet-integration-enable.md)
- [一般的なネットワークの概要](./networking-features.md)