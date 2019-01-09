---
title: ExpressRoute 用の VNet に仮想ネットワーク ゲートウェイを追加する:PowerShell:Azure | Microsoft Docs
description: この記事では、ExpressRoute の作成済みの Resource Manager VNet に VNet ゲートウェイを追加する方法を説明します。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097915"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、Resource Manager デプロイ モデルを使用して作成され、ExpressRoute 構成で使用される予定の VNet 専用です。 ExpressRoute の仮想ネットワーク ゲートウェイとゲートウェイ構成設定の詳細については、[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)を参照してください。 


## <a name="before-beginning"></a>作業を開始する前に
最新の Azure PowerShell コマンドレットがインストールされていることを確認します。 最新のコマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/overview)をご覧ください。

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>次の手順
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。

