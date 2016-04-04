<properties
   pageTitle="Resource Manager と PowerShell を使用した ExpressRoute の仮想ネットワークへの VPN ゲートウェイの追加 | Microsoft Azure"
   description="この記事では、ExpressRoute の作成済みの Resource Manager VNet に VPN ゲートウェイを追加する方法を説明します"
   documentationCenter="na"
   services="expressroute"
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
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# ExpressRoute 構成の Resource Manager VNet への VPN ゲートウェイの追加 

この記事では、ゲートウェイ サブネットを追加し、既存の VNet の VPN ゲートウェイを作成する手順について説明します。この構成の手順は、**Resource Manager のデプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される予定の VNet 専用です。クラシック デプロイ モデルを使用した VNet のゲートウェイの作成の詳細については、「[ExpressRoute 用の Virtual Network の構成](expressroute-howto-vnet-portal-classic.md)」を参照してください。

## 作業を開始する前に

この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。コマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。Azure PowerShell のインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## ゲートウェイが作成されていることの確認

次のコマンドを使用して、ゲートウェイが作成されていることを確認します。

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## 次のステップ

VPN ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。

<!---HONumber=AcomDC_0309_2016-->