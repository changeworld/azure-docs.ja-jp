---
title: Azure の高可用性ポートの概要 | Microsoft Docs
description: 内部ロード バランサーでの高可用性ポートの負荷分散について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: f6e9dd09558a3485629d5b70dd8b68b292427b18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="high-availability-ports-overview"></a>高可用性ポートの概要

内部ロード バランサーを使用するときは、Azure Standard Load Balancer によって、すべてのポートで TCP フローと UDP フローの負荷分散を同時に行うことができます。 

HA ポート規則は負荷分散規則の一種であり、内部 Standard Load Balancer 上に構成されます。 内部 Standard Load Balancer のすべてのポートに到着するすべての TCP フローと UDP フローを負荷分散する単一の規則を指定することで、ロード バランサーの使用を単純化できます。 負荷分散の決定は、フローごとに行われます。 これは、5 タプル接続 (送信元 IP アドレス、送信元ポート、送信先 IP アドレス、送信先ポート、およびプロトコル)に基づいて行われます。

HA ポート機能は、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) の高可用性と拡張性のような、重要なシナリオで役に立ちます。 また、多数のポートを負荷分散する必要がある場合にも役立ちます。 

HA ポート機能は、フロントエンド ポートとバックエンド ポートを **0** に、プロトコルを**すべて**に設定すると構成されます。 その後は、内部 Load Balancer リソースによって、ポート番号に関係なく、すべての TCP フローおよび UDP フローが負荷分散されます。

## <a name="why-use-ha-ports"></a>HA ポートを使う理由

### <a name="nva"></a>ネットワーク仮想アプライアンス

NVA を使うことで、さまざまなセキュリティの脅威から Azure ワークロードを保護できます。 こうしたシナリオで使われる NVA は、信頼性と可用性に優れ、必要に応じてスケールアウトする必要があります。

NVA インスタンスを Azure 内部 Load Balancer のバックエンド プールに追加して、HA ポート Load Balancer 規則を構成するだけで、このような目標を実現できます。

NVA HA シナリオで HA ポートを使うと複数のメリットがあります。
- インスタンスごとの正常性プローブによる、正常なインスタンスへの高速フェールオーバー
- *n* 個のアクティブ インスタンスへのスケールアウトによるパフォーマンスの向上
- *N* アクティブ シナリオとアクティブ/パッシブのシナリオ
- アプライアンスを監視するために Apache Zookeeper ノードのような複雑なソリューションが不要

次の図は、ハブとスポークによる仮想ネットワークのデプロイを示したものです。 トラフィックは、スポークによってハブの仮想ネットワークに強制的にトンネリングされ、NVA を通過した後、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Standard Load Balancer の背後にあります。 すべてのトラフィックを、適切に処理して転送できます。

![HA モードで NVA がデプロイされているハブとスポークの仮想ネットワークの図](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> NVA を使っている場合は、HA ポートを最大限に活用する方法と、サポートされているシナリオについて、それぞれのプロバイダーにご確認ください。

### <a name="load-balancing-large-numbers-of-ports"></a>多数のポートの負荷分散

多数のポートの負荷分散が必要なアプリケーションにも、HA ポートを使うことができます。 HA ポートで内部 [Standard Load Balancer ](load-balancer-standard-overview.md) を使うことにより、これらのシナリオが簡単になります。 ポートごとに 1 つずつ、複数の負荷分散規則を使う代わりに、1 つの負荷分散規則で済みます。

## <a name="region-availability"></a>利用可能なリージョン

HA ポート機能は、すべてのグローバル Azure リージョンで使用できます。

## <a name="supported-configurations"></a>サポートされている構成

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の 1 つの非フローティング IP (非 Direct Server Return) HA ポートの構成

これは、基本的な HA ポートの構成です。 次の構成では、単一のフロントエンド IP アドレスに HA ポートの負荷分散を構成できます。
- Standard Load Balancer を構成するときに、ロード バランサー規則の構成で**[HA ポート]** チェックボックスをオンにします。 
- **[フローティング IP]** が **[無効]** に設定されていることを確認します。

この構成では、現在のロード バランサー リソース用のその他の負荷分散規則の構成と、特定のバックエンド インスタンス セット用のその他の内部ロード バランサー リソースの構成は許可されません。

ただし、この HA ポート規則に加えて、バックエンド インスタンス用のパブリックな Standard Load Balancer を構成できます。

### <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の 1 つのフローティング IP (Direct Server Return) HA ポートの構成

同様に、**HA ポート**と単一のフロントエンドを使用するロード バランサーを構成し、**[フローティング IP]** を **[有効]** に設定できます。 

この構成では、複数のフローティング IP の負荷分散規則またはパブリック ロード バランサー、あるいはその両方を追加できます。 ただし、この構成の上で、非フローティング IP と HA ポートを使用する負荷分散構成は使用できません。

### <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の複数の HA ポート構成

同じバックエンド プールに対して 1 つ以上の HA ポート フロントエンドを構成する必要があるシナリオの場合は、以下によってこれを実現できます。 
- 単一の内部 Standard Load Balancer リソース用の 1 つ以上のフロントエンドのプライベート IP アドレスを構成する。
- 複数の負荷分散規則を構成する。各規則には、1 つの一意のフロントエンド IP アドレスが選択されます。
- **[HA ポート]** オプションを選択し、すべての負荷分散ルールで **[フローティング IP]** を **[有効]** に設定します。

### <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>同じバックエンド インスタンス上で HA ポートとパブリック ロード バランサーを使用する内部ロード バランサー

バックエンド リソース用の **1 つの**パブリック Standard Load Balancer リソースと、HA ポートを使用する単一の Standard Load Balancer を構成できます。

>[!NOTE]
>この機能は、現時点では、Azure Resource Manager テンプレートでのみ使用でき、Azure Portal では使用できません。

## <a name="limitations"></a>制限事項

- HA ポートの構成は、内部ロード バランサーのみで使用可能であり、パブリック ロード バランサーでは使用できません。

- HA ポートの負荷分散規則と非 HA ポートの負荷分散規則の組み合わせはサポートされていません。

- HA ポート機能は、IPv6 では使用できません。

- NVA シナリオのフロー対称は、単一の NIC でのみサポートされます。 [ネットワーク仮想アプライアンス](#nva)の説明と図をご覧ください。 ただし、シナリオで Destination NAT が機能する場合は、それを使用して、内部ロード バランサーが戻りトラフィックを同じ NVA に送信することを確認できます。


## <a name="next-steps"></a>次の手順

- [内部 Standard Load Balancer で HA ポートを構成する](load-balancer-configure-ha-ports.md)
- [Standard Load Balancer の詳細を確認する](load-balancer-standard-overview.md)
