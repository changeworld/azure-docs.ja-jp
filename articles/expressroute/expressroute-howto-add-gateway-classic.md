---
title: 'Azure ExpressRoute: VNet へのゲートウェイの追加: クラシック'
description: ExpressRoute の構成に対して PowerShell を使用してクラシック デプロイ モデルの VNet 用の VNet ゲートウェイを構成します。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bcffd60596f8c2286b91eaf13be3e09bb8bec94f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736409"
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

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)」を参照してください。

