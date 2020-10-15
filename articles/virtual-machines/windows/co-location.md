---
title: VM の併置による待ち時間の短縮
description: Azure VM リソースを併置することで待機時間を短縮する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 9c981e9b829fa10f094370e1e1ffd8b6df967d4b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965779"
---
# <a name="co-locate-resource-for-improved-latency"></a>リソースの併置による待機時間の短縮

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンにインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 


## <a name="proximity-placement-groups"></a>近接通信配置グループ 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次のステップ

Azure PowerShell を使用して[近接通信配置グループ](proximity-placement-groups.md)に VM をデプロイします。

[ネットワーク待機時間をテストする](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)方法について学びます。

[ネットワーク スループットを最適化する](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について学びます。  

[SAP アプリケーションで近接通信配置グループを使用する](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について学びます。