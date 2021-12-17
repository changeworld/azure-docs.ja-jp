---
title: Azure 仮想ネットワークとの統合を有効にする
description: このハウツー記事では、Azure App Service Web アプリで仮想ネットワーク統合を有効にする方法について説明します。
keywords: VNet 統合
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 73a148f205a36091f1df3b35a2d89ac51bf352ef
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894281"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Azure App Service で仮想ネットワーク統合を有効にする

[Azure App Service アプリ](./overview.md)から Azure 仮想ネットワーク (VNet) と統合すると、仮想ネットワーク内のアプリからプライベート リソースにアクセスできます。 VNet 統合機能には 2 つのバリエーションがあります。

* **リージョン仮想ネットワーク統合**: 同じリージョン内の Azure 仮想ネットワークに接続します。 統合する仮想ネットワークには専用のサブネットが必要です。
* **ゲートウェイを必要とする仮想ネットワーク統合**: 他のリージョン内の仮想ネットワークまたは同じリージョン内のクラシック仮想ネットワークに直接接続する場合は、ゲートウェイを必要とする仮想ネットワーク統合を使用する必要があります。

この記事では、リージョン仮想ネットワーク統合をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

VNet 統合機能には、以下のものが必要です。

- [仮想ネットワーク統合をサポートする](./overview-vnet-integration.md) App Service 価格レベル。
- 空のサブネットを持つ同じリージョン内の仮想ネットワーク。

サブネットは Microsoft.Web/serverFarms に委任する必要があります。 統合前に委任が行われなかった場合、プロビジョニング処理によってこの委任が構成されます。 サブネットには、IPv4 `/28` ブロック (16 アドレス) を割り当てる必要があります。 水平方向の最大スケールを可能にするために、少なくとも 64 個のアドレス (IPv4 `/26` ブロック) を使用することをお勧めします。

## <a name="configure-in-the-azure-portal"></a>Azure portal で構成する

1. App Service ポータルで **[ネットワーク]** に移動します。 **[送信トラフィック]** で、 **[VNet 統合]** を選択します。

1. **[Add VNet]\(VNet の追加)** を選択します。

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="VNet 統合の選択を示すスクリーンショット。":::

1. ドロップダウン リストには、お使いのサブスクリプションで同じリージョンに存在するすべての仮想ネットワークが含まれています。 空の既存のサブネットを選択するか、新しいサブネットを作成します。

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="仮想ネットワークの選択を示すスクリーンショット。":::

統合中にアプリは再起動されます。 統合が完了すると、統合されている仮想ネットワークの詳細が表示されます。

## <a name="configure-with-the-azure-cli"></a>Azure CLI を使用して構成する

Azure CLI を使用して仮想ネットワーク統合を構成することもできます。

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> このコマンドでは、サブネットが Microsoft.Web/serverFarms に委任されているかどうかを確認し、構成されていない場合は必要な委任を適用します。 サブネットが構成済みで、これを確認するアクセス許可が付与されていない場合、または仮想ネットワークが別のサブスクリプションにある場合は、 *--skip-delegation-check* パラメーターを使用してこの検証をバイパスできます。

## <a name="configure-with-azure-powershell"></a>Azure PowerShell を使用して構成する

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'
$vNetName = '<vnet-name>'
$integrationSubnetName = '<subnet-name>'
$subscriptionId = '<subscription-guid>'

# Configure VNet Integration
$subnetResourceId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Network/virtualNetworks/$vNetName/subnets/$integrationSubnetName"
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
$webApp.Properties.virtualNetworkSubnetId = $subnetResourceId
$webApp | Set-AzResource -Force
```

## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク統合のルーティングを構成する](./configure-vnet-integration-routing.md)
- [一般的なネットワークの概要](./networking-features.md)