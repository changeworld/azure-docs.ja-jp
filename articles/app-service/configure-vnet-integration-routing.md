---
title: 仮想ネットワーク統合にアプリケーションのルーティングを構成します。
description: この方法に関する記事では、リージョン VNet 統合でのアプリ ルーティングの構成について説明します。
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: d286b8c0bb82c1501cdc77e8097ba6b7d5b2008f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269721"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Azure App Service 仮想ネットワーク統合のルーティングを管理する

アプリケーションのルーティングを構成する場合は、Azure 仮想ネットワーク (VNet) にすべてのトラフィックまたはプライベート トラフィック ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) トラフィックとも呼ばれます) のみをルーティングできます。 この方法に関する記事では、アプリケーションのルーティングを構成する方法について説明します。

## <a name="prerequisites"></a>[前提条件]

アプリは、リージョン VNet 統合機能を使用して既に統合されています。

## <a name="configure-in-the-azure-portal"></a>Azure portal で構成する

次の手順を使用して、ポータルからアプリでの [Route All]\(すべてのルート\) を無効にできます。 

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="[Route All]\(すべてのルート\) が有効":::

1. アプリ ポータルの **[ネットワーク]**  >  **[VNet 統合]** UI に移動します。
1. **[Route All]\(すべてのルート\)** を [無効] にします。
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="[Route All]\(すべてのルート\) を無効にする":::

1. **[はい]** を選択して確定します。

## <a name="configure-with-azure-cli"></a>Azure CLI を使用して構成する

Azure CLI を使用して [すべてをルーティング] を構成することもできます (`az version` は 2.27.0 以降である必要があります)。

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

- [VNet 統合を有効にする](./configure-vnet-integration-enable.md)
- [一般的なネットワークの概要](./networking-features.md)