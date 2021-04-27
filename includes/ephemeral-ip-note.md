---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529576"
---
> [!NOTE]
> パブリック IP アドレスが割り当てられていないか、内部の Basic Azure Load Balancer のバックエンドプールにある Azure Virtual Machines に対しては、Azure によってエフェメラル IP が提供されます。 エフェメラル IP メカニズムは、構成できないアウトバウンド IP アドレスを提供します。 
>
>パブリック IP アドレスが仮想マシンに割り当てられている場合、またはアウトバウンド規則の有無にかかわらず仮想マシンが Standard Load Balancer のバックエンド プールに配置されている場合、エフェメラル IP は無効になります。 [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) ゲートウェイ リソースが仮想マシンのサブネットに割り当てられている場合、エフェメラル IP は無効になります。
>
> Azure でのアウトバウンド接続の詳細については、「[アウトバウンド接続に送信元ネットワーク アドレス変換 (SNAT) を使用する](../articles/load-balancer/load-balancer-outbound-connections.md)」を参照してください。