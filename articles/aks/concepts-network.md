---
title: 概念 - Azure Kubernetes サービス (AKS) におけるネットワーク
description: Azure Kubernetes Service (AKS) におけるネットワークについて説明します。基本ネットワークと高度ネットワーク、イングレス コントローラー、ロード バランサー、および静的 IP アドレスの説明が含まれます。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380887"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念

アプリケーション開発に対するコンテナー ベースのマイクロサービス アプローチでは、アプリケーション コンポーネントは、連携して各自のタスクを処理する必要があります。 Kubernetes には、このアプリケーションの通信を可能にするさまざまなリソースが提供されています。 アプリケーションへの接続と内部または外部への公開を実行できます。 高可用性アプリケーションをビルドするために、アプリケーションの負荷を分散させることができます。 複雑なアプリケーションでは、SSL/TLS 終端または複数のコンポーネントのルーティングのためのイングレス トラフィックの構成が必要な場合があります。 セキュリティ上の理由で、ポッドとノードに対するネットワーク トラフィックまたはポッドとノード間でのネットワーク トラフィックのフロー制限が必要な場合もあります。

この記事では、AKS 内でアプリケーションにネットワークを提供する主要な概念について説明します。

- [サービス](#services)
- [Azure 仮想ネットワーク](#azure-virtual-networks)
- [イングレス コントローラー](#ingress-controllers)
- [ネットワーク ポリシー](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes の基本

アプリケーションへのアクセスまたはアプリケーション コンポーネントの相互通信を可能にするため、Kubernetes には、仮想ネットワークに対する抽象化レイヤーが提供されています。 仮想ネットワークに接続された Kubernetes ノードは、ポッドに対して受信接続と送信接続を実行できます。 これらのネットワーク機能を提供するために、各ノードで *kube-proxy* コンポーネントが実行されます。

Kubernetes では、特定のポートで IP アドレスまたは DNS 名を使用した直接アクセスを許可するように、"*サービス*" によってポッドが論理的にグループ化されます。 "*ロード バランサー*" を使用してトラフィックを分散させることもできます。 "*イングレス コントローラー*" を使用して、アプリケーションのトラフィックの複雑なルーティングも実現できます。 ポッドに対するネットワーク トラフィックのセキュリティ保護とフィルター処理は、*" Kubernetes ネットワーク ポリシー*" によって実行できます。

Azure プラットフォームは、AKS クラスターの仮想ネットワークを簡略化するためにも役立ちます。 Kubernetes ロード バランサーを作成すると、基になる Azure ロード バランサーのリソースが作成されて構成されます。 ポッドへのネットワーク ポートを開くと、対応する Azure ネットワーク セキュリティ グループ規則が構成されます。 HTTP アプリケーション ルーティングでは、新しいイングレス ルートが構成されると、Azure によって "*外部 DNS*" も構成されます。

## <a name="services"></a>サービス

アプリケーション ワークロードのネットワーク構成を簡素化するため、Kubernetes では、"*サービス*" を使用して、一連のポッドを論理的にグループ化してネットワーク接続を行います。 次の種類のサービスを使用できます。

- **クラスター IP** - AKS クラスター内で使用する内部 IP アドレスを作成します。 クラスター内で他のワークロードをサポートする内部専用アプリケーションに適しています。

    ![AKS クラスター内のクラスター IP トラフィック フローを示す図][aks-clusterip]

- **NodePort** - ノード IP アドレスとポートによるアプリケーションへの直接アクセスを許可する、基になるノード上にポート マッピングを作成します。

    ![AKS クラスターでの NodePort トラフィック フローを示す図][aks-nodeport]

- **LoadBalancer** - Azure ロード バランサー リソースを作成し、外部 IP アドレスを構成し、要求されたポッドをロード バランサーのバックエンド プールに接続します。 顧客のトラフィックによるアプリケーションへのアクセスを許可するために、目的のポート上に負荷分散規則が作成されます。 

    ![AKS クラスター内の負荷分散トラフィック フローを示す図][aks-loadbalancer]

    制御と受信トラフィックのルーティングを追加するため、[イングレス コントローラー](#ingress-controllers)を代わりに使用できます。

- **ExternalName** - 特定の DNS エントリを作成して、アプリケーションに簡単にアクセスできるようにします。

ロード バランサーとサービスの IP アドレスは動的に割り当てることができます。または、使用する既存の静的 IP アドレスを指定できます。 内部と外部の静的 IP アドレスの両方を割り当てることができます。 この既存の静的 IP アドレスは、多くの場合、DNS エントリに関連付けられています。

"*内部*" ロード バランサーと "*外部*" ロード バランサーの両方を作成できます。 内部ロード バランサーにはプライベート IP アドレスのみが割り当てられるため、インターネットからアクセスすることはできません。

## <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

AKS では、次の 2 つのネットワーク モデルのいずれかを使用するクラスターをデプロイできます。

- "*基本*" ネットワーク - AKS クラスターのデプロイ時にネットワーク リソースが作成され、構成されます。
- "*高度*" ネットワーク - AKS クラスターは既存の仮想ネットワーク リソースに接続され、構成されます。

### <a name="basic-networking"></a>基本ネットワーク

"*基本*" ネットワーク オプションは、AKS クラスターを作成するための既定の構成です。 クラスターとポッドのネットワーク構成はAzure プラットフォームが管理します。 基本ネットワークは、仮想ネットワークのカスタム構成を必要としないデプロイに適しています。 基本ネットワークでは、AKS クラスターに割り当てるサブネット名や IP アドレスの範囲などのネットワーク構成を定義することはできません。

基本ネットワーク用に構成された AKS クラスターのノードは、[kubenet][kubenet] Kubernetes プラグインを使用します。

基本ネットワークには次の機能があります。

- Azure Load Balancer を使用して、Kubernetes サービスを外部または内部に公開できます。
- ポッドはパブリック インターネット上のリソースにアクセスできます。

### <a name="advanced-networking"></a>高度ネットワーク

"*高度*" ネットワークでは、構成する Azure 仮想ネットワーク内にポッドに配置します。 この仮想ネットワークでは、他の Azure リソースへの自動接続が行われ、豊富な機能セットが統合されます。 高度ネットワークは、既存のサブネットと接続を使用するような特別な仮想ネットワーク構成を必要とするデプロイに適しています。 高度ネットワークでは、サブネット名と IP アドレスの範囲を定義できます。

高度ネットワーク用に構成された AKS クラスターのノードは、[Azure Container Networking Interface (CNI)][cni-networking] Kubernetes プラグインを使用します。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図][advanced-networking-diagram]

高度ネットワークには基本ネットワークの機能に加え、次の機能があります。

- AKS クラスターを既存の Azure 仮想ネットワークにデプロイするか、クラスター用の新しい仮想ネットワークとサブネットを作成します。
- クラスター内のすべてのポッドに、仮想ネットワーク内の IP アドレスが割り当てられます。 ポッドは、クラスター内の他のポッドに加え、仮想ネットワーク内の他のノードと直接通信できます。
- ポッドは、ピアリングされた仮想ネットワーク内の他のサービスに接続でき、ExpressRoute とサイト間 (S2S) VPN 接続経由でオンプレミス ネットワークと接続することもできます。 ポッドにはオンプレミスからも到達できます。
- サービス エンドポイントが有効なサブネット内のポッドは、Azure サービス (Azure Storage や SQL DB など) に安全に接続できます。
- ユーザー定義のルート (UDR) を使用して、ポッドのトラフィックをネットワーク仮想アプライアンスにルーティングできます。

詳細については、[AKS クラスターの高度ネットワーク構成][aks-configure-advanced-networking]に関する説明を参照してください。

## <a name="ingress-controllers"></a>イングレス コントローラー

LoadBalancer 型のサービスを作成すると、基になる Azure ロード バランサー リソースが作成されます。 ロード バランサーは、特定のポートでサービス内のポッドにトラフィックを分散するように構成されます。 LoadBalancer はレイヤー 4 でのみ動作します。サービスは実際のアプリケーションを認識せず、追加のルーティングを考慮することはできません。

"*イングレス コントローラー*" はレイヤー 7 で動作し、インテリジェントなルールを使用してアプリケーションのトラフィックを分散させることができます。 イングレス コントローラーの一般的な用途は、受信 URL に基づいて別のアプリケーションに HTTP トラフィックをルーティングすることです。

![AKS クラスターでのイングレス トラフィック フローを示す図][aks-ingress]

AKS では、NGINX などを使用してイングレス リソースを作成するか、AKS の HTTP アプリケーションのルーティング機能を使用できます。 AKS クラスターで HTTP アプリケーションのルーティングを有効にすると、Azure プラットフォームがイングレス コントローラーと*External-DNS* コントローラーを作成します。 新しいイングレス リソースが Kubernetes に作成されると、必要な DNS A レコードがクラスター固有のDNS ゾーンに作成されます。 詳細については、「[HTTP アプリケーション ルーティング][aks-http-routing]」を参照してください。

イングレスのもう 1 つの一般的な機能は、SSL/TLS 終端です。 HTTPS 経由でアクセスされる大規模な Web アプリケーションでは、アプリケーション自体の中ではなく、イングレス リソースによって TLS 終端を処理できます。 TLS 証明書の自動生成と構成を提供することで、Let's Encrypt などのプロバイダーを使用するイングレス リソースを構成できます。 Let's Encrypt を使用した NGINX イングレス コントローラーの構成の詳細については、[イングレスと TLS][aks-ingress-tls]に関する記事を参照してください。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループは、AKS ノードなどの VM に対するトラフィックをフィルター処理します。 LoadBalancer などのサービスを作成すると、必要なネットワーク セキュリティ グループ規則が Azure プラットフォームによって自動的に構成されます。 AKS クラスター内のポッドに対するトラフィックをフィルター処理するネットワーク セキュリティ グループ規則は手動で構成しないでください。 Kubernetes サービス マニフェストの一部として必要なポートと転送を定義し、適切なルールの作成または更新は Azure プラットフォームに任せてください。

SSH などのトラフィック向けの既定のネットワーク セキュリティ グループ規則が存在します。 これらの既定の規則は、クラスターの管理とアクセスに関するトラブルシューティング用のものです。 これらの既定の規則を削除すると、AKS の管理で問題が発生し、サービス レベル目標 (SLO) を達成できなくなる可能性があります。

## <a name="next-steps"></a>次の手順

AKS ネットワーク を開始するには、[AKS クラスターの高度ネットワークの作成と構成][aks-configure-advanced-networking]に関する記事を参照してください。

Kubernetes と AKS の中心概念の追加情報については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS のアクセスと ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md