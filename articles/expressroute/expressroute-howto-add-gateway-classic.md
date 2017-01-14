---
title: "PowerShell を使用して ExpressRoute 用の VNet ゲートウェイを構成する | Microsoft Docs"
description: "ExpressRoute の構成に対して PowerShell を使用してクラシック デプロイ モデルの VNet 用の VNet ゲートウェイを構成します。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 4acb64838288d36f0dc1b1eb9736b00faef21a0c
ms.openlocfilehash: 4b05e12c2b30f1e8aa3efc7cd47cb79c4958dc1e


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>クラシック デプロイ モデルと PowerShell を使用して ExpressRoute 用の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
> * [PowerShell - クラシック](expressroute-howto-add-gateway-classic.md)
> 
> 

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、 **クラシック デプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される VNet 専用です。 

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>作業を開始する前に
この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。 コマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。 Azure PowerShell のインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)」を参照してください。




<!--HONumber=Dec16_HO1-->


