---
title: 回線の帯域幅のアップグレードについて | Azure ExpressRoute
description: この記事では、ExpressRoute 回線の帯域幅をアップグレードするためのベスト プラクティスについて説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: duau
ms.openlocfilehash: 75570beb243fca802ecebebf34edf02b3d8ca9a5
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006493"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅のアップグレードについて

ExpressRoute は、Microsoft のグローバル ネットワークへの専用接続ならびにプライベート接続です。 接続は、ExpressRoute パートナーのネットワーク、または Microsoft Enterprise Edge (MSEE) デバイスへの直接接続によって容易になります。 物理接続を構成してテストしたら、ExpressRoute 回線を作成し、ピアリングを構成することによって、レイヤー 2 とレイヤー 3 の接続を有効にすることができます。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>回線の帯域幅をアップグレードする

回線の帯域幅をアップグレードするには、ExpressRoute Direct または ExpressRoute パートナーに、アップグレードを成功させるために[十分な使用可能な帯域幅](#considerations)がある必要があります。

容量が使用可能な場合は、次の方法を使用して回線をアップグレードできます。

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量に関する考慮事項

### <a name="insufficient-capacity-for-physical-connection"></a>物理接続のための容量が不足している

ExpressRoute 回線は、Microsoft と ExpressRoute パートナーの間の物理的な接続上に作成されます。 物理的な接続の容量は固定されます。 回線のサイズを大きくできない場合は、既存の回線の基になる物理接続に、アップグレードするための容量が存在しないということです。 回線サイズを変更するには、新しい回線を作成する必要があります。

新しい ExpressRoute 回線を正常に作成したら、既存の仮想ネットワークをこの回線にリンクする必要があります。 その後、古い回線をプロビジョニング解除する前に、新しい ExpressRoute 回線の接続をテストおよび検証できます。 ここでは、ダウンタイムと、運用環境の作業負荷の中断を最小限に抑えるために推奨される移行手順について説明します。

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>不十分な ExpressRoute パートナーの帯域幅

容量エラーが原因で新しい ExpressRoute 回線を作成できない場合、 これは、この ExpressRoute パートナーに、このピアリングの場所で Microsoft に接続する容量がないことを意味します。 容量の増加を依頼するために、ExpressRoute パートナーにお問い合わせください。

新しい容量がプロビジョニングされたら、「[回線の帯域幅をアップグレードする](#upgrade)」セクションに記載されている手順を使用して、新しい回線の作成、接続の構成、および古い回線の削除を行うことができます。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>不十分な ExpressRoute Direct の帯域幅

ExpressRoute Direct に十分な容量がない場合は、2 つの選択肢があります。 不要になった ExpressRoute Direct リソースに関連付けられている回線を削除するか、新しい ExpressRoute Direct リソースを作成することができます。 ExpressRoute Direct リソースの管理に関するガイダンスについては、[ExpressRoute Direct を構成する方法](how-to-expressroute-direct-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [回線の作成と変更](expressroute-howto-circuit-portal-resource-manager.md)
* [ピアリング構成の作成と変更](expressroute-howto-routing-portal-resource-manager.md)
* [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-portal-resource-manager.md)
