<properties
   pageTitle="Resource Manager と PowerShell を使用した ExpressRoute の仮想ネットワークへの VNet ゲートウェイの追加 | Microsoft Azure"
   description="この記事では、ExpressRoute の作成済みの Resource Manager VNet に VNet ゲートウェイを追加する方法を説明します"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="charwen"/>


# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Resource Manager と PowerShell を使用して ExpressRoute 用の仮想ネットワーク ゲートウェイを構成する


> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - クラシック](expressroute-howto-add-gateway-classic.md)


この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、 **Resource Manager のデプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される予定の VNet 専用です。 

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>作業を開始する前に

この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。 コマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。 Azure PowerShell のインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>次のステップ

VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。



<!--HONumber=Oct16_HO2-->


