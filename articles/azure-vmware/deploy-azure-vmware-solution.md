---
title: Azure VMware Solution をデプロイして構成する
description: 計画ステージで収集した情報を使用して、Azure VMware Solution プライベート クラウドをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy22q1, devx-track-azurecli
ms.date: 07/09/2021
ms.openlocfilehash: 30c6360e49da2574edd87c639811aac4b66d5e9e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438203"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution をデプロイして構成する

この記事では、[計画セクション](production-ready-deployment-steps.md)の情報を使用して、Azure VMware Solution をデプロイおよび構成します。 

この図は、Azure VMware Solution のデプロイ ワークフローを示したものです。 

:::image type="content" source="media/deploy-azure-vmware-solution-workflow.png" alt-text="Azure VMware Solution のデプロイ ワークフローの図。" lightbox="media/deploy-azure-vmware-solution-workflow.png" border="false":::

## <a name="step-1-register-the-microsoftavs-resource-provider"></a>手順 1. **Microsoft.AVS** リソース プロバイダーを登録する

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="step-2-create-an-azure-vmware-solution-private-cloud"></a>手順 2. Azure VMware Solution のプライベート クラウドを作成する

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>この手順全体の概要については、ビデオ「[Azure VMware Solution: デプロイ](https://www.youtube.com/embed/gng7JjxgayI)」をご覧ください。


## <a name="step-3-connect-to-azure-virtual-network-with-expressroute"></a>手順 3. ExpressRoute を使用して Azure Virtual Network に接続する

計画フェーズでは、"*既存*" または "*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを使用するかを決めました。  

:::image type="content" source="media/connect-expressroute-vnet-workflow.png" alt-text="Azure VMware Solution で Azure Virtual Network を ExpressRoute に接続するワークフローを示す図。" border="false":::

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>新しい ExpressRoute 仮想ネットワーク ゲートウェイを使用する

>[!IMPORTANT]
>まだ仮想ネットワーク ゲートウェイの **ない** ゲートウェイ サブネットを含む仮想ネットワークを用意する必要があります。

| 状況 | THEN  |
| --- | --- |
| まだ仮想ネットワークがない     |  次のものを作成します。<ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network">仮想ネットワーク</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>        |
| ゲートウェイ サブネットを **含まない** 仮想ネットワークが既に存在する   | 次のものを作成します。 <ol><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>          |
| ゲートウェイ サブネットを **含んだ** 仮想ネットワークが既に存在する | 次のものを作成します。 <ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>    |


### <a name="use-an-existing-virtual-network-gateway"></a>既存の仮想ネットワーク ゲートウェイを使用する

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="step-4-validate-the-connection"></a>手順 4. 接続の検証

ExpressRoute の終点となる Azure Virtual Network と Azure VMware Solution のプライベート クラウドとを結ぶ接続が必要です。 

1. Azure VMware Solution の ExpressRoute の終点となる Azure Virtual Network 内にある[仮想マシン](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)を使用します (「[手順 3. ExpressRoute を使用して Azure Virtual Network に接続する](#step-3-connect-to-azure-virtual-network-with-expressroute)」を参照)。  

   1. Azure [portal](https://portal.azure.com) にログインします。

   1. 実行状態の VM に移動し、 **[設定]** の **[ネットワーク]** を選択して、ネットワーク インターフェイス リソースを選択します。

      :::image type="content" source="../virtual-network/media/diagnose-network-routing-problem/view-nics.png" alt-text="仮想ネットワークのインターフェイス設定を示すスクリーンショット。":::

   1. 左側で、**[Effective routes]\(有効なルート\)** を選択します。 デプロイ フェーズで入力した `/22` CIDR ブロックの範囲内に含まれるアドレス プレフィックスが一覧表示されます。

1. vCenter と NSX-T Manager の両方にログインしたい場合は、Web ブラウザーを開いて、ネットワーク ルートの検証に使用したものと同じ仮想マシンにログインします。  

   Azure portal で、vCenter および NSX-T Manager コンソールの IP アドレスと資格情報を識別できます。  プライベート クラウドを選択し、 **[管理]**  >  **[ID]** を選択します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を示すスクリーンショット。" border="true":::


## <a name="next-steps"></a>次のステップ

次のセクションでは、ExpressRoute 経由で Azure VMware Solution をオンプレミス ネットワークに接続します。

> [!div class="nextstepaction"]
> [オンプレミスの環境に接続する](tutorial-expressroute-global-reach-private-cloud.md)
