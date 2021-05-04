---
title: Azure VMware Solution をデプロイして構成する
description: 計画ステージで収集した情報を使用して、Azure VMware Solution プライベート クラウドをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 04/23/2021
ms.openlocfilehash: 9a9f01788ccfaa7f304d71897efa3bcb0511d8fd
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126383"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution をデプロイして構成する

この記事では、[計画セクション](production-ready-deployment-steps.md)の情報を使用して、Azure VMware Solution をデプロイおよび構成します。 

>[!IMPORTANT]
>情報をまだ定義していない場合は、続行する前に[計画セクション](production-ready-deployment-steps.md)に戻ってください。

## <a name="step-1-register-the-microsoftavs-resource-provider"></a>手順 1. **Microsoft.AVS** リソース プロバイダーを登録する

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="step-2-create-an-azure-vmware-solution-private-cloud"></a>手順 2. Azure VMware Solution のプライベート クラウドを作成する

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>この手順全体の概要については、ビデオ「[Azure VMware Solution: デプロイ](https://www.youtube.com/embed/gng7JjxgayI)」をご覧ください。


## <a name="step-3-connect-to-azure-virtual-network-with-expressroute"></a>手順 3. ExpressRoute を使用して Azure Virtual Network に接続する

計画フェーズでは、"*既存*" の ExpressRoute 仮想ネットワーク ゲートウェイを使用するか、"*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを使用するかを決めました。  

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>新しい ExpressRoute 仮想ネットワーク ゲートウェイを使用する

>[!IMPORTANT]
>まだ仮想ネットワーク ゲートウェイの **ない** ゲートウェイ サブネットを含む仮想ネットワークを用意する必要があります。

| 状況 | THEN  |
| --- | --- |
| まだ仮想ネットワークがない     |  次のものを作成します。<ul><li><a href="tutorial-configure-networking.md#create-a-virtual-network">仮想ネットワーク</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li></ul>        |
| ゲートウェイ サブネットを **含まない** 仮想ネットワークが既に存在する   | 次のものを作成します。 <ul><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">仮想ネットワーク ゲートウェイ</a></li></ul>          |
| ゲートウェイ サブネットを **含んだ** 仮想ネットワークが既に存在する | [仮想ネットワーク ゲートウェイの作成](tutorial-configure-networking.md#create-a-virtual-network-gateway)   |


### <a name="use-an-existing-virtual-network-gateway"></a>既存の仮想ネットワーク ゲートウェイを使用する

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="step-4-validate-the-connection"></a>手順 4. 接続の検証

ExpressRoute の終点となる Azure Virtual Network と Azure VMware Solution のプライベート クラウドとを結ぶ接続が必要です。 

1. Azure VMware Solution の ExpressRoute の終点となる Azure Virtual Network 内にある[仮想マシン](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)を使用します (「[手順 3. ExpressRoute を使用して Azure Virtual Network に接続する](#step-3-connect-to-azure-virtual-network-with-expressroute)」を参照)。  

   1. Azure [portal](https://portal.azure.com) にログインします。
   2. 実行状態の VM に移動し、 **[設定]** の **[ネットワーク]** を選択して、ネットワーク インターフェイス リソースを選択します。
      ![ネットワーク インターフェイスを表示する](../virtual-network/media/diagnose-network-routing-problem/view-nics.png)
   4. 左側で、**[Effective routes]\(有効なルート\)** を選択します。 デプロイ フェーズで入力した `/22` CIDR ブロックの範囲内に含まれるアドレス プレフィックスが一覧表示されます。

1. vCenter と NSX-T Manager の両方にログインしたい場合は、Web ブラウザーを開いて、ネットワーク ルートの検証に使用したものと同じ仮想マシンにログインします。  

   Azure portal で、vCenter および NSX-T Manager コンソールの IP アドレスと資格情報を識別できます。  プライベート クラウドを選択し、 **[管理]**  >  **[ID]** を選択します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報のスクリーンショット。" border="true":::


## <a name="next-steps"></a>次のステップ

次のセクションでは、ExpressRoute 経由で Azure VMware Solution をオンプレミス ネットワークに接続します。
> [!div class="nextstepaction"]
> [オンプレミスの環境に接続する](tutorial-expressroute-global-reach-private-cloud.md)