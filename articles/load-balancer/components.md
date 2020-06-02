---
title: Azure Load Balancer のコンポーネント
description: Azure Load Balancer のコンポーネントの概要
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684990"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer のコンポーネント

Azure Load Balancer は、いくつかの重要なコンポーネントで構成されます。 これらは、ご利用のサブスクリプションで、Azure portal、Azure CLI、Azure PowerShell、またはテンプレートを使用して構成できます。

## <a name="frontend-ip-configuration"></a>フロントエンド IP の構成 <a name = "frontend-ip-configurations"></a>

Azure Load Balancer の IP アドレスです。 クライアントにとっての接続点となります。 これらの IP アドレスは、次のいずれかになります。

- **パブリック IP アドレス**
- **プライベート IP アドレス**

IP アドレスの特質によって、作成されるロード バランサーの**種類**が決まります。 プライベート IP アドレスを選択した場合は、内部ロード バランサーが作成されます。 パブリック IP アドレスを選択した場合は、パブリック ロード バランサーが作成されます。

|  | パブリック ロード バランサー  | 内部ロード バランサー |
| ---------- | ---------- | ---------- |
| フロントエンド IP の構成| パブリック IP アドレス | プライベート IP アドレス|
| 説明 | パブリック ロード バランサーは、受信トラフィックのパブリック IP およびポートを、VM のプライベート IP およびポートにマップします。 ロード バランサーは、VM からの応答トラフィックについてはその逆にマップします。 負荷分散規則を適用することで、特定の種類のトラフィックを複数の VM やサービスに分散できます。 たとえば、複数の Web サーバー間で Web 要求のトラフィックの負荷を分散できます。| 内部ロード バランサーは、仮想ネットワーク内のリソースにトラフィックを分散させます。 負荷分散の対象となる、仮想ネットワークのフロントエンド IP アドレスは、Azure によってアクセスが制限されます。 フロントエンド IP アドレスと仮想ネットワークは、インターネット エンドポイントに直接公開されることはありません。 社内の基幹業務アプリケーションは Azure で実行され、Azure 内またはオンプレミス リソースからアクセスされます。 |
| サポートされている SKU | Basic、Standard | Basic、Standard |

![階層化されたロード バランサーの例](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>バックエンド プール

受信要求を処理する仮想マシンのグループまたは仮想マシン スケール セット内のインスタンスのグループ。 コスト効果に優れた方法でスケーリングして大量の受信トラフィックに対処するために、コンピューティングのガイドラインでは通常、バックエンド プールにインスタンスを追加することが推奨されます。

ロード バランサーは、インスタンスがスケールアップまたはスケールダウンされると、自動再構成を通じてすぐに自身を再構成します。 バックエンド プールの VM を追加または削除すると、追加操作なしに、ロード バランサーが再構成されます。 バックエンド プールのスコープは、仮想ネットワーク内の任意の仮想マシンです。

バックエンド プールの設計方法を検討するときは、個々のバックエンド プール リソースを最小限の数に設計して、管理操作の期間を最適化します。 データ プレーンのパフォーマンスやスケールに違いはありません。

## <a name="health-probes"></a>正常性プローブ

正常性プローブは、バックエンド プール内のインスタンスの正常性状態を判断するために使用されます。 Load Balancer を作成する場合は、Load Balancer によってインスタンスが正常であるかどうかを判断し、トラフィックをルーティングするために使用できる正常性プローブを構成する必要があります。

正常性プローブには、異常しきい値を定義できます。 プローブが応答できない場合、Load Balancer によって、異常なインスタンスへの新しい接続の送信が停止されます。 プローブの失敗は、既存の接続には影響しません。 接続は、アプリケーションが以下を実行するまで続行されます。

- フローを終了する
- アイドル タイムアウトが発生する
- VM がシャットダウンする

Load Balancer は、エンドポイントにさまざまな種類の正常性プローブを提供します。TCP、HTTP、HTTPS です。

Basic Load Balancer では、HTTPS プローブはサポートされていません。 Basic Load Balancer によって、すべての TCP 接続 (確立済み接続を含む) が終了されます。

## <a name="load-balancing-rules"></a>負荷分散規則

負荷分散規則は、バックエンド プール内の**すべて**のインスタンスに受信トラフィックを分散する方法を定義するために使用されます。 負荷分散規則は、フロントエンドの特定の IP 構成およびポートをバックエンドの複数の IP アドレスおよびポートにマッピングします。

たとえば、フロントエンド IP のポート 80 (または別のポート) のトラフィックをすべてのバックエンド インスタンスのポート 80 にルーティングする場合は、負荷分散規則を使用してこれを実現します。

## <a name="inbound-nat-rules"></a>受信 NAT のルール

受信 NAT のルールによって、選択したフロントエンド IP アドレスとポートの組み合わせに送信された受信トラフィックが、バックエンド プール内の**固有**の仮想マシンまたはインスタンスに転送されます。 ポート フォワーディングも、負荷分散と同じハッシュベースの分散によって実行されます。

たとえば、バックエンド プール内の個々の VM インスタンスにリモート デスクトップ プロトコル (RDP) セッションや SSH (Secure Shell) セッションを使用する場合です。 複数の内部エンドポイントを、同じフロントエンド IP アドレスのポートにマッピングできます。 フロントエンド IP アドレスを使用すると、ジャンプ ボックスを追加しなくても VM をリモート管理できます。

## <a name="outbound-rules"></a>アウトバウンド規則

すべての仮想マシン、またはバックエンド プールによって識別されたインスタンスを対象とするアウトバウンド ネットワーク アドレス変換 (NAT) は、アウトバウンド規則によって構成します。 これにより、バックエンド内のインスタンスがインターネットやその他のエンドポイントに通信 (送信) できるようになります。

Basic Load Balancer では、アウトバウンド規則がサポートされていません。

## <a name="next-steps"></a>次のステップ

- Load Balancer の使用を開始するには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参照してください。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
- [パブリック IP アドレス](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)について学習する。
- [プライベート IP アドレス](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)について学習する。
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)の使用について学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [アイドル時の TCP リセット](load-balancer-tcp-reset.md)について学習する。
- [HA ポート負荷分散ルールでの Standard Load Balancer](load-balancer-ha-ports-overview.md) について学習する。
- [複数のフロントエンド IP 構成での Load Balancer](load-balancer-multivip-overview.md) の使用について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)の詳細を確認する。
- [プローブの種類](load-balancer-custom-probe-overview.md#types)について学習する。
- [ロード バランサーの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)について詳細を確認する。
- [ポート フォワーディング](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)の使用について学習する。
- [ロード バランサーのアウトバウンド規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)について詳細を確認する。
