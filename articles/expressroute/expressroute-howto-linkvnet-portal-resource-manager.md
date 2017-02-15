---
title: "Resource Manager デプロイメント モデルと Azure Portal を使用した ExpressRoute 回線への仮想ネットワークのリンク | Microsoft Docs"
description: "このドキュメントでは、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b3a8cdab9e609994b1c4c4d50e9571718e8091de
ms.openlocfilehash: 8043f0d5a4c9fbd301e7565e7d62be09dd77abd8


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute 回線に仮想ネットワークを接続する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [クラシック - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

この記事では、Resource Manager デプロイメント モデルと Azure ポータルを使用して Azure ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>構成の前提条件
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
* アクティブな ExpressRoute 回線が必要です。
  
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md) し、接続プロバイダー経由で回線を有効にしてください。
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-portal-resource-manager.md) に関する記事を参照してください。
  * Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)を作成する手順 (手順 1. ～ 5. のみ) に従ってください。

最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。 ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクしたり、さらに多くの仮想ネットワークを ExpressRoute 回線にリンクしたりすることができます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する
### <a name="to-create-a-connection"></a>接続を作成するには
1. ExpressRoute 回線と Azure プライベート ピアリングが正常に構成されていることを確認します。 指示に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、[ルーティングを構成](expressroute-howto-routing-arm.md)します。 ExpressRoute 回線は次の図のようになります。
   
    ![ExpressRoute 回線のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
   > [!NOTE]
   > ピアリングをレイヤー 3 プロバイダーが構成した場合、BGP の構成情報は表示されません。 回線がプロビジョニング済み状態の場合は、接続を作成できます。
   > 
   > 
2. ExpressRoute 回線に仮想ネットワーク ゲートウェイをリンクする接続のプロビジョニングを開始できるようになりました。 **[接続]**  >  **[追加]** をクリックし、**[接続の追加]** ブレードを開き、値を構成します。 次の参照例を参照してください。

    ![接続追加のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


1. 接続が正常に構成されると、接続オブジェクトにより、接続の情報が表示されます。
   
    ![接続オブジェクトのスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>接続を削除するには
接続を削除するには、接続のブレードで **[削除]** アイコンを選択します。

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する
現時点では、Azure ポータルを使用して、サブスクリプション間で仮想ネットワークを接続することはできません。 ただし、PowerShell を使用して、接続することはできます。 詳細については、 [PowerShell](expressroute-howto-linkvnet-arm.md) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。




<!--HONumber=Dec16_HO2-->


