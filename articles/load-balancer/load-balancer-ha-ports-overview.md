---
title: "Azure の高可用性ポートの概要 | Microsoft Docs"
description: "内部 Load Balancer 上の高可用性ポートの負荷分散について説明します"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e41c70b982b97c6aab7b6c0322c193c61370a26
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="high-availability-ports-overview"></a>高可用性ポートの概要

Azure Load Balancer の Standard SKU は高可用性 (HA) ポートを導入しています。HA ポートは、すべてのサポートされるプロトコルについて、すべてのポートのトラフィックを分散する機能です。 ユーザーは、内部 Load Balancer を構成するときに、フロントエンドとバックエンドのポートを **0** に設定し、プロトコルを **all** に設定する HA ポート ルールを構成し、すべてのトラフィックが内部 Load Balancer を経由するようにすることができます。

負荷分散アルゴリズムは同じままです。また、分散先は 5 つのタプル <ソース IP アドレス、ソース ポート、分散先 IP アドレス、分散先ポート、プロトコル> に基づいて選択されます。 ただし、この構成では、すべての使用できるトラフィックを処理するために 1 つの LB ルールを使用できるため、構成の複雑さが軽減されるだけでなく、追加できる負荷分散ルールの最大数によって制限が課せられます。

HA ポートで可能になる重要なシナリオの 1 つとして、Azure 仮想ネットワークへのネットワーク仮想アプライアンスの高可用性デプロイがあります。 また、HA ポートで可能になる一般的なシナリオとして、ポートの範囲に対する負荷分散があります。 

## <a name="why-use-high-ha-ports"></a>高い HA ポートを使用する理由

Azure ユーザーは、さまざまなセキュリティの脅威からセキュリティでワークロードを保護するために、ネットワーク仮想アプライアンス (NVA) に大きく依存しています。 また、NVA は信頼性が高く、高可用である必要があります。  

HA ポートを使用すると、Zookeeper のように複雑なソリューションが不要になるため、NVA HA シナリオの複雑さが軽減されます。また、迅速なフェールオーバーとスケールアウト オプションで信頼性が向上します。 Azure 内部のロード バランサーのバックエンド プールに NVA を追加し、HA ポートのロード バランサー ルールを構成することで、NVA HA を実現できるようになりました。

次の例は、ハブとスポークの仮想ネットワーク デプロイを示しています。スポークによってトラフィックをハブの仮想ネットワークへのトンネリングを強制し、NVA を経由してから、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Load Balancer の背後にあるため、それに従ってすべてのトラフィックを処理し、転送します。 

![HA ポートの例](./media/load-balancer-ha-ports-overview/nvaha.png)

図 1 - HA モードで NVA がデプロイされているハブとスポークの仮想ネットワーク

## <a name="caveats"></a>注意事項

HA ポートでサポートされる構成または例外は次のとおりです。

- 1 つのフロントエンド ipConfiguration は、HA ポート (すべてのポート) が指定された 1 つの DSR Load Balancer ルールを持つことができます。または、HA ポート (すべてのポート) が指定された 1 つの DSR 以外の Load Balancer ルールを持つことができます。 両方を持つことはできません。
- 1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された DSR 以外のロード バランサー ルールを 1 つのみ持つことができます。 この ipconfig には、他のルールを構成できません。
- 個々のフロントエンド IP 構成のすべてが一意であれば、1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された 1 つまたは複数の DSR ロード バランサー ルールを持つことができます。
- すべての Load Balancer ルールが HA ポート (DSR のみ) の場合、またはすべてのルールが HA ポート以外 (DSR および DSR 以外) の場合、同じバックエンド プールを示す複数の Load Balancer ルールが共存することができます。 このような 2 つの LB ルールは、HA ポートと HA ポート以外のルールの組み合わせがある場合は共存できません。
- IPv6 対応テナントでは HA ポートを使用できません。


## <a name="next-steps"></a>次のステップ

[内部 Load Balancer で HA ポートを構成する](load-balancer-configure-ha-ports.md)


