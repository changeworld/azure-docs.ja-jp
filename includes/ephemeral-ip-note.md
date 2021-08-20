---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: d6ee1a0c747681d6da85ae1275a09d6ad5443b27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472429"
---
> [!NOTE]
> パブリック IP アドレスが割り当てられていないか、内部の Basic Azure Load Balancer のバックエンドプールにある Azure Virtual Machines に対しては、Azure によってデフォルト送信アクセス IP が提供されます。 デフォルト送信アクセス IP メカニズムは、構成できないアウトバウンド IP アドレスを提供します。 
>
>パブリック IP アドレスが仮想マシンに割り当てられている場合、またはアウトバウンド規則の有無にかかわらず仮想マシンが Standard Load Balancer のバックエンド プールに配置されている場合、デフォルト送信アクセス IP は無効になります。 [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) ゲートウェイ リソースが仮想マシンのサブネットに割り当てられている場合、デフォルト送信アクセス IP は無効になります。
>
> Azure でのアウトバウンド接続の詳細については、「[アウトバウンド接続に送信元ネットワーク アドレス変換 (SNAT) を使用する](../articles/load-balancer/load-balancer-outbound-connections.md)」を参照してください。