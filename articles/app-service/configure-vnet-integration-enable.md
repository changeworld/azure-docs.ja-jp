---
title: Azure 仮想ネットワークとの統合を有効にします。
description: この方法に関する記事では、App Service Web アプリで仮想ネットワーク統合を有効にする方法について説明します
keywords: VNet 統合
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 549c92b932e0d589aec2490512fc18713fa71546
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269921"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Azure App Service で仮想ネットワーク統合を有効にする

[App Service アプリ](./overview.md)から Azure 仮想ネットワーク (VNet) と統合すると、仮想ネットワーク内のアプリからプライベート リソースにアクセスできます。 VNet 統合機能には 2 つのバリエーションがあります。

* リージョン VNet 統合: 同じリージョン内の Azure 仮想ネットワークに接続します。 統合する VNet には専用のサブネットが必要です。
* ゲートウェイを必要とする VNet 統合: 他のリージョン内の VNet または同じリージョン内のクラシック仮想ネットワークに直接接続する場合は、ゲートウェイを必要とする VNet 統合を使用する必要があります。

この方法に関する記事では、リージョン VNet 統合を設定する方法について説明します。

## <a name="prerequisites"></a>[前提条件]

VNet 統合には次のものが必要となります。
- [VNet 統合をサポートする](./overview-vnet-integration.md) App Service 価格レベル。
- 空のサブネットを持つ同じリージョン内の仮想ネットワーク。

サブネットは Microsoft.Web/serverFarms に委任する必要があります。 統合前に委任が行われなかった場合、プロビジョニング処理によってこの委任が構成されます。 サブネットには、IPv4 `/28` ブロック (16 アドレス) を割り当てる必要があります。 実際には、水平方向の最大スケールを可能にするために、少なくとも 64 個のアドレス (IPv4 `/26` ブロック) を使用することをお勧めします。

## <a name="configure-in-the-azure-portal"></a>Azure portal で構成する

1. App Service ポータルで **[ネットワーク]** の UI に移動します。 **[送信トラフィック]** で、 **[VNet 統合]** を選択します。

1. **[Add VNet]\(VNet の追加)** を選択します。

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="Vnet 統合を選択する":::

1. ドロップダウン リストには、お使いのサブスクリプションで同じリージョンに存在するすべての仮想ネットワークが含まれています。

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="VNet の選択":::

    * 空の既存のサブネットを選択するか、新しいサブネットを作成します。

統合中にアプリは再起動されます。 統合が完了すると、統合されている VNet の詳細が表示されます。

## <a name="configure-with-azure-cli"></a>Azure CLI を使用して構成する

Azure CLI を使用して VNet 統合を構成することもできます。

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> このコマンドでは、サブネットが Microsoft.Web/serverFarms に委任されているかどうかを確認し、これが構成されていない場合は必要な委任を適用します。 既に構成済みで、これを確認するアクセス許可が付与されていない場合、または仮想ネットワークが別のサブスクリプションにある場合は、`--skip-delegation-check` パラメーターを使用してこの検証をバイパスできます。

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

- [VNet 統合ルーティングを構成する](./configure-vnet-integration-routing.md)
- [一般的なネットワークの概要](./networking-features.md)