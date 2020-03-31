---
title: Microsoft Azure VM を併置する
description: Azure VM リソースを併置することで待機時間を短縮する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231875"
---
# <a name="co-locate-resource-for-improved-latency"></a>リソースの併置による待機時間の短縮

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンにインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 


## <a name="proximity-placement-groups"></a>近接通信配置グループ 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次のステップ

Azure PowerShell を使用して[近接通信配置グループ](proximity-placement-groups.md)に VM をデプロイします。

[ネットワーク待機時間をテストする](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について学びます。

[ネットワーク スループットを最適化する](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について学びます。  

[SAP アプリケーションで近接通信配置グループを使用する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について学びます。