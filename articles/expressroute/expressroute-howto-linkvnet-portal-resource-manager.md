<properties
   pageTitle="Resource Manager デプロイメント モデルと Azure ポータルを使用した ExpressRoute 回線への仮想ネットワークのリンク | Microsoft Azure"
   description="このドキュメントでは、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# ExpressRoute 回線への仮想ネットワークのリンク

> [AZURE.SELECTOR]
- [Azure ポータル - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - クラシック](expressroute-howto-linkvnet-classic.md)



この記事では、Resource Manager デプロイメント モデルと Azure ポータルを使用して Azure ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。


**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 構成の前提条件

- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
- アクティブな ExpressRoute 回線が必要です。
	- 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてください。

	- 回線用に Azure プライベート ピアリングが構成されていることを確認してください。ルーティング手順については、[ルーティングの構成](expressroute-howto-routing-portal-resource-manager.md)に関する記事を参照してください。

	- Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。

	- 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。[VPN ゲートウェイ](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)を作成する手順 (手順 1. ～ 5. のみ) に従ってください。

最大 10 個の仮想ネットワークを ExpressRoute 回線に接続できます。ExpressRoute 回線はすべて同じ地理的リージョンにある必要があります。ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線に多数の仮想ネットワークをリンクできます。Premium アドオンの詳細については、[FAQ](expressroute-faqs.md) を確認してください。

## 同じサブスクリプション内の仮想ネットワークを回線に接続する


### 接続を作成するには

1. ExpressRoute 回線と Azure プライベート ピアリングが正常に構成されていることを確認します。指示に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、[ルーティングを構成](expressroute-howto-routing-arm.md)します。ExpressRoute 回線は次の図のようになります。

	![ExpressRoute 回線のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] ピアリングをレイヤー 3 プロバイダーが構成した場合、BGP の構成情報は表示されません。回線がプロビジョニング済み状態の場合は、接続を作成できます。

2. ExpressRoute 回線に仮想ネットワーク ゲートウェイをリンクする接続のプロビジョニングを開始できるようになりました。**[接続]**、**[追加]** の順にクリックし、**[接続の追加]** ブレードを開き、値を構成します。次の参照例を参照してください。


	![接続追加のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)


3. 接続が正常に構成されると、接続オブジェクトにより、接続の情報が表示されます。

	![接続オブジェクトのスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### 接続を削除するには

接続を削除するには、接続のブレードで **[削除]** アイコンを選択します。

## 別のサブスクリプション内の仮想ネットワークを回線に接続する

現時点では、Azure ポータルを使用して、サブスクリプション間で仮想ネットワークを接続することはできません。ただし、PowerShell を使用して、接続することはできます。詳細については、[PowerShell](expressroute-howto-linkvnet-arm.md) に関する記事を参照してください。

## 次のステップ

ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

<!---HONumber=AcomDC_0720_2016-->