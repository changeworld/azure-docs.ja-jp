---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 35b4f49638d64a1de322fc712602a2d6cbad290a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019215"
---
> [!NOTE]
> パブリック IP アドレスが割り当てられていないか、内部の Basic Azure Load Balancer のバックエンドプールにある Azure Virtual Machines に対しては、Azure によってデフォルト送信アクセス IP が提供されます。 デフォルト送信アクセス IP メカニズムは、構成できないアウトバウンド IP アドレスを提供します。 
>
> 既定の送信アクセスの詳細については、「[Azure での既定の送信アクセス](../articles/virtual-network/ip-services/default-outbound-access.md)」を参照してください。
>
>パブリック IP アドレスが仮想マシンに割り当てられている場合、またはアウトバウンド規則の有無にかかわらず仮想マシンが Standard Load Balancer のバックエンド プールに配置されている場合、既定の送信アクセス IP は無効になります。 [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) ゲートウェイ リソースが仮想マシンのサブネットに割り当てられている場合、デフォルト送信アクセス IP は無効になります。
>
> フレキシブル オーケストレーション モードの Virtual Machine Scale Sets によって作成された仮想マシンには、既定の送信アクセスがありません。
>
> Azure でのアウトバウンド接続の詳細については、「[アウトバウンド接続に送信元ネットワーク アドレス変換 (SNAT) を使用する](../articles/load-balancer/load-balancer-outbound-connections.md)」を参照してください。