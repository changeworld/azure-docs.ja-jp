---
title: Azure Private Link を使用して、ネットワークを Azure Monitor に接続する
description: ネットワークを Azure Monitor に安全に接続するように Azure Monitor Private Link スコープを設定します。
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727234"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Azure Private Link を使用して、ネットワークを Azure Monitor に接続する

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS (サービスとしてのプラットフォーム) サービスを仮想ネットワークに安全にリンクできます。 多くのサービスでは、リソースごとにエンドポイントを設定するだけです。 ただし、Azure Monitor は、相互に連携してワークロードを監視する、相互接続されたさまざまなサービスの集まりです。 

## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Monitor にプライベートに接続する
- 承認されたプライベート ネットワーク経由でのみ監視データにアクセスできるようにする
- プライベート エンドポイント経由で接続する特定の Azure Monitor リソースを定義して、プライベート ネットワーク経由のデータ流出を防ぐ
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Monitor に安全に接続する
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

Azure Monitor Private Link スコープ (AMPLS) によって、プライベート エンドポイント (およびそれらが含まれている VNet) が 1 つ以上の Azure Monitor リソース (Log Analytics ワークスペースと Application Insights コンポーネント) に接続されます。

![基本的なリソース トポロジの図](./media/private-link-security/private-link-basic-topology.png)

* VNet のプライベート エンドポイントを使用すると、これらのエンドポイントのパブリック IP を使用するのではなく、ネットワークのプールのプライベート IP を介して Azure Monitor エンドポイントに接続することができます。 これにより、不要な送信トラフィックへの VNet を開かなくても、Azure Monitor リソースを使用し続けることができます。 
* プライベート エンドポイントから Azure Monitor リソースへのトラフィックは、パブリック ネットワークにルーティングされるのではなく、Microsoft Azure のバックボーンを経由します。 
* パブリック ネットワークからの取り込みとクエリを許可または拒否するように、各ワークスペースまたはコンポーネントを構成できます。 これによりリソース レベルの保護が提供されるため、特定のリソースへのトラフィックを制御できます。

> [!NOTE]
> 1 つの Azure Monitor リソースは複数の AMPLS に属することはできますが、1 つの VNet を複数の AMPLS に接続することはできません。 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Azure Monitor Private Link と DNS: 全か無か
一部の Azure Monitor サービスでは、グローバル エンドポイントが使用されます。つまり、ワークスペースまたはコンポーネントを対象とする要求が処理されます。 Private Link 接続を設定すると、トラフィックを Private Link 経由で送信するために、Azure Monitor エンドポイントをプライベート IP にマップするように DNS が更新されます。 グローバル エンドポイントに関しては、(1 つのリソースに対してでも) Private Link を設定すると、すべてのリソースへのトラフィックに影響します。 つまり、特定のコンポーネントまたはワークスペースのみの Private Link 接続を作成することはできません。

#### <a name="global-endpoints"></a>グローバル エンドポイント
最も重要なことですが、下のグローバル エンドポイントへのトラフィックは Private Link 経由で送信されます。
* Application Insights エンドポイント (Application Insights エンドポイントへのインジェスト、ライブ メトリック、プロファイラー、デバッガーなどを処理するエンドポイント) はすべてグローバルです。
* クエリ エンドポイント (Application Insights と Log Analytics の両方のリソースに対するクエリを処理するエンドポイント) はグローバルです。

つまり、実質的には、すべての Application Insights トラフィックが Private Link に送信され、Application Insights と Log Analytics の両方のリソースに対するすべてのクエリが Private Link に送信されます。

AMPLS に追加されない Application Insights リソースへのトラフィックは、Private Link 検証に合格せず、失敗します。

![全か無かの動作の図](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>リソース固有のエンドポイント
クエリ エンドポイントを除くすべての Log Analytics エンドポイントがワークスペース固有です。 そのため、特定の Log Analytics ワークスペースに Private Link を作成しても、他のワークスペースへのインジェスト (または他の) トラフィックには影響しません。ここでは、引き続きパブリック Log Analytics エンドポイントが使用されます。 ただし、すべてのクエリは Private Link を介して送信されます。

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>Azure Monitor Private Link は、同じ DNS を共有しているすべてのネットワークに適用されます
一部のネットワークは、複数の VNet または接続された他のネットワークで構成されます。 これらのネットワークで同じ DNS を共有している場合は、これらのいずれかで Private Link を設定すると、DNS が更新され、すべてのネットワークのトラフィックに影響します。 上記の "全か無か" の動作のため、これに注意することが特に重要です。

![複数の VNet での DNS オーバーライドの図](./media/private-link-security/dns-overrides-multiple-vnets.png)

上の図では、VNet 10.0.1.x が最初に AMPLS1 に接続され、Azure Monitor のグローバル エンドポイントがその範囲の IP にマッピングされます。 その後、VNet 10.0.2.x が AMPLS2 に接続され、"*同じグローバル エンドポイント*" の DNS マッピングがその範囲の IP でオーバーライドされます。 これらの VNet はピアリングされていないため、最初の VNet はこれらのエンドポイントに接続できません。


## <a name="next-steps"></a>次のステップ
- [Private Link のセットアップ設計](private-link-design.md)
- [Private Link の構成](private-link-configure.md)方法について確認します

<h3><a id="connect-to-a-private-endpoint"></a></h3>
