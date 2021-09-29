---
title: Azure VMware Solution をデプロイして構成する
description: 計画ステージで収集した情報を使用して、Azure VMware Solution プライベート クラウドをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy22q1, devx-track-azurecli
ms.date: 07/28/2021
ms.openlocfilehash: 0df1634d047dfe6abfaa717fd10b75b99f100076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623933"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution をデプロイして構成する

[デプロイを計画](plan-private-cloud-deployment.md)したら、Azure VMware Solution のプライベート クラウドをデプロイして構成します。 

この図は、Azure VMware Solution のデプロイ ワークフローを示したものです。 

:::image type="content" source="media/deploy-azure-vmware-solution-workflow.png" alt-text="Azure VMware Solution のデプロイ ワークフローを示す図。" lightbox="media/deploy-azure-vmware-solution-workflow.png" border="false":::

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * リソース プロバイダーを登録してプライベート クラウドを作成する
> * 新規または既存の ExpressRoute 仮想ネットワーク ゲートウェイに接続する
> * ネットワーク接続を検証する

完了したら、最後に推奨される次の手順に従って、この入門ガイドの手順に進みます。

## <a name="register-the-microsoftavs-resource-provider"></a>Microsoft.AVS リソース プロバイダーを登録する

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="create-an-azure-vmware-solution-private-cloud"></a>Azure VMware Solution のプライベート クラウドを作成する

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>この手順全体の概要については、ビデオ「[Azure VMware Solution: デプロイ](https://www.youtube.com/embed/gng7JjxgayI)」をご覧ください。


## <a name="connect-to-azure-virtual-network-with-expressroute"></a>ExpressRoute を使用して Azure Virtual Network に接続する

計画フェーズでは、"*既存*" または "*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを使用するかを決めました。  

:::image type="content" source="media/connect-expressroute-vnet-workflow.png" alt-text="Azure VMware Solution で Azure Virtual Network を ExpressRoute に接続するワークフローを示す図。" border="false":::

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>新しい ExpressRoute 仮想ネットワーク ゲートウェイを使用する

>[!IMPORTANT]
>まだ仮想ネットワーク ゲートウェイの **ない** ゲートウェイ サブネットを含む仮想ネットワークを用意する必要があります。

| 状況 | THEN  |
| --- | --- |
| まだ仮想ネットワークがない     |  次のものを作成します。<ol><li><a href="tutorial-configure-networking.md#create-a-vnet-manually">仮想ネットワーク</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>        |
| ゲートウェイ サブネットを **含まない** 仮想ネットワークが既に存在する   | 次のものを作成します。 <ol><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>          |
| ゲートウェイ サブネットを **含んだ** 仮想ネットワークが既に存在する | 次のものを作成します。 <ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">ExpressRoute をゲートウェイに接続する</a></li></ol>    |

### <a name="use-an-existing-virtual-network-gateway"></a>既存の仮想ネットワーク ゲートウェイを使用する

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="validate-the-connection"></a>接続の検証

ExpressRoute の終点となる Azure Virtual Network と Azure VMware Solution のプライベート クラウドとを結ぶ接続が必要です。 

1. Azure VMware Solution の ExpressRoute の終点となる Azure Virtual Network 内にある[仮想マシン](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)を使用します。 詳細については、「[ExpressRoute を使用して Azure Virtual Network に接続する](#connect-to-azure-virtual-network-with-expressroute)」を参照してください。  

   1. Azure [portal](https://portal.azure.com) にログインします。

   1. 実行状態の VM に移動し、 **[設定]** の **[ネットワーク]** を選択して、ネットワーク インターフェイス リソースを選択します。

      :::image type="content" source="../virtual-network/media/diagnose-network-routing-problem/view-nics.png" alt-text="仮想ネットワークのインターフェイス設定を示すスクリーンショット。":::

   1. 左側で、**[Effective routes]\(有効なルート\)** を選択します。 デプロイ フェーズで入力した `/22` CIDR ブロックの範囲内に含まれるアドレス プレフィックスが一覧表示されます。

1. vCenter と NSX-T Manager の両方にログインしたい場合は、Web ブラウザーを開いて、ネットワーク ルートの検証に使用したものと同じ仮想マシンにログインします。  

   Azure portal で、vCenter および NSX-T Manager コンソールの IP アドレスと資格情報を識別できます。  プライベート クラウドを選択し、 **[管理]**  >  **[ID]** を選択します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を示すスクリーンショット。" border="true":::


## <a name="next-steps"></a>次のステップ

次のチュートリアルでは、ExpressRoute 経由で Azure VMware Solution をオンプレミス ネットワークに接続します。

> [!div class="nextstepaction"]
> [オンプレミスの環境に接続する](tutorial-expressroute-global-reach-private-cloud.md)
