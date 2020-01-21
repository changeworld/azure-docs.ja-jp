---
title: Azure Load Balancer の概要
titleSuffix: Azure Load Balancer
description: Azure Load Balancer の機能の概要、アーキテクチャ、実装。 Load Balancer の動作とクラウドでの使用方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045370"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer の概要

"*負荷分散*" とは、負荷 (受信ネットワーク トラフィック) をバックエンド リソースまたはサーバーのグループ全体に均等に分散することです。 Azure には、[さまざまな負荷分散方法](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)が用意されており、必要に応じた方法を選択することができます。 このドキュメントでは、Azure Load Balancer について取り上げます。

Azure Load Balancer は、開放型システム間相互接続 (OSI) モデルのレイヤー 4 で動作します。 クライアントにとっての単一接続点となります。 ロード バランサーは、ロード バランサーのフロントエンドに到着した新しいインバウンド フローを、バックエンド プールのインスタンスに分配します。 これらのフローは、構成された負荷分散規則と正常性プローブに従っています。 バックエンド プール インスタンスには、Azure Virtual Machines か、仮想マシン スケール セット内のインスタンスを使用できます。


**[パブリック ロード バランサー](./concepts-limitations.md#publicloadbalancer)** は、仮想ネットワーク内の仮想マシン (VM) にアウトバウンド接続を提供できます。 これらの接続は、プライベート IP アドレスをパブリック IP アドレスに変換することで実現されます。 パブリック ロード バランサーは、インターネット トラフィックを VM に負荷分散する目的で使用されます。

**[内部 (プライベート) ロード バランサー](./concepts-limitations.md#internalloadbalancer)** は、フロントエンドのみでプライベート IP が必要な場合に使用されます。 内部ロード バランサーは、仮想ネットワーク内でトラフィックを負荷分散させるために使用されます。 ハイブリッド シナリオでは、オンプレミスのネットワークからロード バランサー フロントエンドにアクセスできます。

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*図:パブリック ロード バランサーと内部ロード バランサーの両方を使った、多層アプリケーションの負荷分散*

個々のロード バランサー コンポーネントの詳細については、[Azure Load Balancer のコンポーネントと制限](./concepts-limitations.md)に関するページを参照してください

>[!NOTE]
> Microsoft では、[Standard Load Balancer](./load-balancer-standard-overview.md) を推奨しています。
スタンドアロン VM、可用性セット、および仮想マシン スケール セットは、どちらか一方の SKU にのみ接続でき、両方には接続できません。 パブリック IP アドレスで使うときは、Load Balancer とパブリック IP アドレスの SKU が一致していなければなりません。 Load Balancer とパブリック IP の SKU は変更できません。

## <a name="why-use-azure-load-balancer"></a>Azure Load Balancer を使用する理由
Azure Load Balancer を使用すると、アプリケーションをスケーリングしたり、高可用性サービスを作成したりすることができます。 ロード バランサーは、インバウンドとアウトバウンドの両方のシナリオをサポートしています。 ロード バランサーは、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

Azure Load Balancer を使用して実現できる主なシナリオは、次のとおりです。

- **[内部](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** および **[外部](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** トラフィックを Azure 仮想マシンに負荷分散する。

- ゾーンの **[内部で](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** 、および複数のゾーンに **[わたって](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** リソースを分散することによって、可用性を向上させる。

- Azure 仮想マシンの **[アウトバウンド接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** を構成する。

- **[正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** を使用して、負荷分散されたリソースを監視する。

- **[ポート転送](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** を使用して、仮想ネットワーク内の仮想マシンにパブリック IP アドレスとポートでアクセスする。

- **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** の **[負荷分散](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** のサポートを有効にする。

- Azure Load Balancer の **[メトリックと診断](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** を **[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** で活用する。

- **[複数のポート、複数の IP アドレス、またはその両方](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** にサービスを負荷分散する。

- 複数の Azure リージョンにわたる **[内部](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** および **[外部](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** のロード バランサー リソースに移動する。

- **[HA ポート](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** を使用して、TCP と UDP のフローをすべてのポートに同時に負荷分散する。

## <a name="pricing"></a>価格

Standard Load Balancer の使用量は課金されます。

* 構成された負荷分散および送信規則の数。 受信 NAT 規則は規則の合計数にカウントされません。
* 規則に関係なくインバウンドとアウトバウンドで処理されたデータの量。

Standard Load Balancer の価格の情報については、「[Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)」を参照してください。

Basic Load Balancer は無料で提供されます。

## <a name="sla"></a>SLA

Standard Load Balancer の SLA については、「[Load Balancer の SLA](https://aka.ms/lbsla)」を参照してください。

## <a name="next-steps"></a>次のステップ

Load Balancer の使用を開始するには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参照してください。

Azure Load Balancer の制限とコンポーネントの詳細については、[Azure Load Balancer の概念と制限](./concepts-limitations.md)に関するページを参照してください
