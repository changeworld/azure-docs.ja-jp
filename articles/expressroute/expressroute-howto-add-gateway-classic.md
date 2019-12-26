---
title: 'Azure ExpressRoute: VNet へのゲートウェイの追加: クラシック'
description: ExpressRoute の構成に対して PowerShell を使用してクラシック デプロイ モデルの VNet 用の VNet ゲートウェイを構成します。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928061"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する (クラシック)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、**クラシック デプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される VNet 専用です。 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>作業を開始する前に
この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します。

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>次の手順
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)」を参照してください。

