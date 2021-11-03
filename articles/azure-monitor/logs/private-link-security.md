---
title: Azure Private Link を使用して、ネットワークを Azure Monitor に接続する
description: ネットワークを Azure Monitor に安全に接続するように Azure Monitor Private Link スコープを設定します。
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 872a9c1f58974f1394286a6dd8f2a1c35892930c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078182"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Azure Private Link を使用して、ネットワークを Azure Monitor に接続する

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、プライベート エンドポイントを使用することにより、Azure PaaS (サービスとしてのプラットフォーム) リソースを仮想ネットワークに安全にリンクできます。 Azure Monitor は、相互に連携してワークロードを監視する、相互接続されたさまざまなサービスの集まりです。 Azure Monitor Private Link は、監視ネットワークの境界を定義して、プライベート エンドポイントを Azure Monitor リソースのセットに接続します。 このセットは、Azure Monitor Private Link スコープ (AMPLS) と呼ばれています。


## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Monitor にプライベートに接続する
- 承認されたプライベート ネットワーク経由でのみ監視データにアクセスできるようにする
- プライベート エンドポイント経由で接続する特定の Azure Monitor リソースを定義して、プライベート ネットワーク経由のデータ流出を防ぐ
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Monitor に安全に接続する
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

### <a name="overview"></a>概要
Azure Monitor Private Link スコープによって、プライベート エンドポイント (およびそれらが含まれている VNet) が 1 つ以上の Azure Monitor リソース (Log Analytics ワークスペースと Application Insights コンポーネント) に接続されます。

![基本的なリソース トポロジの図](./media/private-link-security/private-link-basic-topology.png)

* Azure Monitor Private Link によりプライベート エンドポイントが Azure Monitor リソースのセット、つまり、Log Analytics ワークスペースと Application Insights リソースに接続されます。 このセットは、Azure Monitor Private Link スコープ (AMPLS) と呼ばれています。
* VNet のプライベート エンドポイントを使用すると、これらのエンドポイントのパブリック IP を使用するのではなく、ネットワークのプールのプライベート IP を介して Azure Monitor エンドポイントに接続することができます。 これにより、不要な送信トラフィックへの VNet を開かなくても、Azure Monitor リソースを使用し続けることができます。 
* プライベート エンドポイントから Azure Monitor リソースへのトラフィックは、パブリック ネットワークにルーティングされるのではなく、Microsoft Azure のバックボーンを経由します。
* 優先アクセス モードを使用して、Azure Monitor Private Link リソースへのトラフィックのみを許可するか、Private Link リソースと Private Link 以外のリソース (AMPLS 外のリソース) の両方へのアクセスを許可するように、Private Link スコープ (あるいはそれに接続している特定のネットワーク) を構成できます
* パブリック ネットワークからの取り込みとクエリを許可または拒否するように、各ワークスペースまたはコンポーネントを構成できます。 これによりリソース レベルの保護が提供されるため、特定のリソースへのトラフィックを制御できます。

> [!NOTE]
> VNet は、1 つの AMPLS にのみ接続できます。これには、Private Link 経由で到達できる最大 50 のリソースがリストされています。

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Azure Monitor Private Link と DNS の依存関係
Private Link 接続を設定すると、トラフィックをその Private Link 経由で送信するため、Azure Monitor エンドポイントをプライベート IP にマップするように DNS ゾーンが設定されます。 Azure Monitor では、リソース固有のエンドポイントと、複数のワークスペース/コンポーネントに対するトラフィックを処理するリージョン エンドポイントまたはグローバル エンドポイントの両方が使用されます。 リージョン エンドポイントおよびグローバル エンドポイントについては、Private Link を設定すると (単一のリソースであっても)、**すべての** リソースに対するトラフィックを制御する DNS マッピングが影響を受けます。 つまり、1 つの Private Link を設定するだけで、すべてのワークスペースまたはコンポーネントに対するトラフィックが影響を受ける可能性があります。

#### <a name="global-endpoints"></a>グローバル エンドポイント
最も重要なことですが、下のグローバル エンドポイントへのトラフィックは Private Link 経由で送信されます。
* Application Insights エンドポイント (Application Insights エンドポイントへのインジェスト、ライブ メトリック、プロファイラー、デバッガーなどを処理するエンドポイント) はすべてグローバルです。
* クエリ エンドポイント (Application Insights と Log Analytics の両方のリソースに対するクエリを処理するエンドポイント) はグローバルです。

つまり、実質的には、すべての Application Insights トラフィックが Private Link に送信され、Application Insights と Log Analytics の両方のリソースに対するすべてのクエリが Private Link に送信されます。

AMPLS に追加されない Application Insights リソースへのトラフィックは、Private Link 検証に合格せず、失敗します。

#### <a name="resource-specific-endpoints"></a>リソース固有のエンドポイント
クエリ エンドポイントを除くすべての Log Analytics エンドポイントがワークスペース固有です。 そのため、特定の Log Analytics ワークスペースに Private Link を作成しても、他のワークスペースへのインジェストには影響せず、パブリック エンドポイントが引き続き使用されます。


> [!NOTE]
> 同一の DNS を共有するすべてのネットワークに対して、1 つの AMPLS のみを作成してください。 複数の AMPLS リソースを作成すると、Azure Monitor DNS ゾーンが互いにオーバーライドされ、既存の環境が破壊されます。

### <a name="private-link-access-modes-private-only-vs-open"></a>Private Link のアクセス モード: Private Only と Open
「[Azure Monitor Private Link と DNS の依存関係](#azure-monitor-private-link-relies-on-your-dns)」で説明されているように、同一の DNS を共有するすべてのネットワークに対して、1 つの AMPLS リソースのみを作成する必要があります。 その結果、1 つのグローバル DNS またはリージョン DNS を使用する組織では、実際には 1 つの Private Link で、すべてのグローバル ネットワークまたはリージョン ネットワーク全体のすべての Azure Monitor リソースへのトラフィックを管理することになります。

2021 年 9 月より前に作成された Private Link の場合、これは次のことを意味します。 
* ログ インジェストは、その AMPLS 内のリソースに対してのみ機能します。 他のすべてのリソースへのインジェストは、サブスクリプションまたはテナントに関係なく、同じ DNS を共有するすべてのネットワークで拒否されます。
* クエリの動作はよりオープンで、AMPLS に含まれないリソースに対してもクエリ要求が到達できます。 これは、AMPLS に含まれないリソースに対する顧客のクエリが中断されるのを回避し、リソース中心のクエリで完全な結果セットを返せるようにすることを意図したものでした。

しかし、この動作は、一部の顧客には制限が厳しすぎ (AMPLS に含まれないリソースへのインジェストが中断されるため)、一部の顧客には制限が緩すぎ (AMPLS に含まれないリソースに対するクエリの実行が許可されるため) て、混乱を招きやすいことが判明しました。

このため、2021 年 9 月以降に作成された Private Link では、Private Link がネットワーク トラフィックに与える影響を明示的に設定する、新しい必須の AMPLS 設定が設けられています。 新しい AMPLS リソースを作成しようとすると、インジェストとクエリに関して個別に目的のアクセス モードを選択するように要求されます。 
* Private Only モード - Private Link リソースに対するトラフィックのみを許可します
* Open モード - AMPLS 内のリソースと通信するのには Private Link を使用しますが、他のリソースに対するトラフィックも引き続き許可されます。 詳細については、「[プライベート リンクをネットワークに適用する方法を制御する](./private-link-design.md#control-how-private-links-apply-to-your-networks)」を参照してください。

> [!NOTE]
> Log Analytics インジェストではリソース固有のエンドポイントが使用されます。 そのため、AMPLS アクセス モードに準拠しません。 **Log Analytics インジェスト要求で AMPLS の外部にあるワークスペースにアクセスできないようにするには、AMPLS アクセス モードには関係なく、パブリック エンドポイントへのトラフィックをブロックするようにネットワーク ファイアウォールを設定します**。

## <a name="next-steps"></a>次のステップ
- [Private Link のセットアップ設計](private-link-design.md)
- [Private Link の構成](private-link-configure.md)方法について確認します
- カスタムログとお客様が管理するキー (CMK) の [プライベートストレージ](private-storage.md) について説明します。
<h3><a id="connect-to-a-private-endpoint"></a></h3>
