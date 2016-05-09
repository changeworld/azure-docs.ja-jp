<properties
   pageTitle="PowerShell を使用して ExpressRoute 用の VNet ゲートウェイを構成する | Microsoft Azure"
   description="ExpressRoute の構成に対して PowerShell を使用してクラシック デプロイ モデルの VNet 用の VNet ゲートウェイを構成します。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charwen"/>

# クラシック デプロイ モデルと PowerShell を使用して ExpressRoute 用の仮想ネットワーク ゲートウェイを構成する

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - クラシック](expressroute-howto-add-gateway-classic.md)

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。この構成の手順は、**クラシック デプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される VNet 専用です。

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 作業を開始する前に

この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。コマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。Azure PowerShell のインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

	
## 次のステップ

VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)」を参照してください。

<!---HONumber=AcomDC_0427_2016-->