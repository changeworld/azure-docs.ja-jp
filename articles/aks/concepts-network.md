---
title: 概念 - Azure Kubernetes サービス (AKS) におけるネットワーク
description: Azure Kubernetes Service (AKS) におけるネットワークについて説明します。kubenet と Azure CNI ネットワーク、イングレス コントローラー、ロード バランサー、静的 IP アドレスの説明が含まれます。
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105342"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念

アプリケーション開発に対するコンテナー ベースのマイクロサービス アプローチでは、アプリケーション コンポーネントが連携してそれぞれのタスクを処理します。 Kubernetes は、この連携を可能にするさまざまなリソースを提供します。
* アプリケーションへの接続と内部または外部への公開を実行できます。 
* アプリケーションを負荷分散することにより、可用性の高いアプリケーションを構築することができます。 
* より複雑なアプリケーションの場合、SSL/TLS 終端または複数のコンポーネントのルーティングのためのイングレス トラフィックを構成できます。 
* セキュリティ上の理由から、ポッドとノードに対するネットワーク トラフィックまたはポッドとノード間でのネットワーク トラフィックのフローを制限できます。

この記事では、AKS 内でアプリケーションにネットワークを提供する主要な概念について説明します。

- [サービス](#services)
- [Azure 仮想ネットワーク](#azure-virtual-networks)
- [イングレス コントローラー](#ingress-controllers)
- [ネットワーク ポリシー](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes の基本

アプリケーションへのアクセスまたはアプリケーション コンポーネント間のアクセスを可能にするため、Kubernetes には、仮想ネットワークに対する抽象化レイヤーが用意されています。 Kubernetes ノードは仮想ネットワークに接続し、ポッドに対して受信接続と送信接続を提供します。 これらのネットワーク機能を提供するために、各ノードで *kube-proxy* コンポーネントが実行されます。

Kubernetes では、以下のことを行えます。
* "*サービス*" によってポッドが論理的にグループ化され、IP アドレスまたは DNS 名を使用して特定のポートに直接アクセスできます。 
* "*ロード バランサー*" を使用してトラフィックを分散できます。 
* "*イングレス コントローラー*" を使用して、アプリケーションのトラフィックの複雑なルーティングも実現できます。 
* ポッドに対するネットワーク トラフィックのセキュリティ保護とフィルター処理は、 *" Kubernetes ネットワーク ポリシー*" によって実行できます。

Azure プラットフォームによりさらに、AKS クラスターの仮想ネットワークが簡略化されます。 Kubernetes ロード バランサーを作成すると、基になる Azure ロード バランサーのリソースも作成されて構成されます。 ポッドへのネットワーク ポートを開くと、対応する Azure ネットワーク セキュリティ グループ規則が構成されます。 HTTP アプリケーション ルーティングでは、新しいイングレス ルートが構成されると、Azure によって "*外部 DNS*" も構成されます。

## <a name="services"></a>サービス

アプリケーション ワークロードのネットワーク構成を簡素化するため、Kubernetes では、"*サービス*" を使用して、一連のポッドを論理的にグループ化してネットワーク接続を行います。 次の種類のサービスを使用できます。

- **クラスターの IP** 
  
  AKS クラスター内で使用する内部 IP アドレスを作成します。 クラスター内で他のワークロードをサポートする内部専用アプリケーションに適しています。

    ![AKS クラスター内のクラスター IP トラフィック フローを示す図][aks-clusterip]

- **NodePort** 

  ノード IP アドレスとポートによるアプリケーションへの直接アクセスを可能にする、基になるノード上にポート マッピングを作成します。

    ![AKS クラスターでの NodePort トラフィック フローを示す図][aks-nodeport]

- **LoadBalancer** 

  Azure ロード バランサー リソースを作成し、外部 IP アドレスを構成し、要求されたポッドをロード バランサーのバックエンド プールに接続します。 顧客のトラフィックによるアプリケーションへのアクセスを許可するために、目的のポート上に負荷分散規則が作成されます。 

    ![AKS クラスター内の負荷分散トラフィック フローを示す図][aks-loadbalancer]

    受信トラフィックの制御とルーティングを追加するために、代わりに[イングレス コントローラー](#ingress-controllers)を使用できます。

- **ExternalName** 

  特定の DNS エントリを作成して、アプリケーションに簡単にアクセスできるようにします。

ロード バランサーまたはサービスの IP アドレスを動的に割り当てるか、または、既存の静的 IP アドレスを指定することができます。 内部と外部の静的 IP アドレスの両方を割り当てることができます。 既存の静的 IP アドレスは、多くの場合、DNS エントリに関連付けられています。

"*内部*" と "*外部*" 両方のロード バランサーを作成できます。 内部ロード バランサーにはプライベート IP アドレスのみが割り当てられるため、インターネットからそれらにアクセスすることはできません。

## <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

AKS では、次の 2 つのネットワーク モデルのいずれかを使用するクラスターをデプロイできます。

- *Kubenet* ネットワーク

  ネットワーク リソースは通常、AKS クラスターのデプロイ時に作成され、構成されます。

- *Azure Container Networking Interface (CNI)* ネットワーク
 
  AKS クラスターは、既存の仮想ネットワーク リソースと構成に接続されます。

### <a name="kubenet-basic-networking"></a>Kubenet (基本) ネットワーク

*kubenet* ネットワーク オプションは、AKS クラスターを作成するための既定の構成です。 *kubernet* を使用する場合:
1. ノードでは Azure 仮想ネットワークのサブネットから IP アドレスを受け取ります。 
1. ポッドでは、ノードの Azure 仮想ネットワーク サブネットとは論理的に異なるアドレス空間から IP アドレスを受け取ります。 
1. その後、ポッドで Azure 仮想ネットワークのリソースに到達できるように、ネットワーク アドレス変換 (NAT) が構成されます。 
1. トラフィックの発信元 IP アドレスは、ノードのプライマリ IP アドレスに変換されます。

ノードでは、[kubenet][kubenet] Kubernetes プラグインを使用します。 次の操作を行います。
* Azure プラットフォームにより、自動的に仮想ネットワークを作成および構成する。または、 
* AKS クラスターを既存の仮想ネットワーク サブネットにデプロイすることを選択する。 

ルーティング可能な IP アドレスを受け取るのはノードのみであることに注意してください。 ポッドでは NAT を使用して、AKS クラスター外の他のリソースと通信します。 この方法では、ポッドで使用するためにネットワーク空間で予約する必要がある IP アドレスの数が減ります。

詳細については、[AKS クラスター用の kubenet ネットワークの構成][aks-configure-kubenet-networking]に関するページを参照してください。

### <a name="azure-cni-advanced-networking"></a>Azure CNI (高度) ネットワーク

Azure CNI を使用して、すべてのポッドでサブネットから IP アドレスを取得します。ポッドには直接アクセスできます。 これらの IP アドレスは、事前に計画し、ネットワーク空間全体で一意にする必要があります。 各ノードには、サポートされるポッドの最大数に対する構成パラメーターがあります。 ノードごとにそれと同じ数の IP アドレスが、事前に予約されます。 計画がないと、アプリケーション需要の拡大に伴い、IP アドレスが不足したり、より大きなサブネットでのクラスターの再構築が必要になったりする可能性があります。

kubenet とは異なり、同じ仮想ネットワーク内のエンドポイントへのトラフィックは、ノードのプライマリ IP に NAT 処理されません。 仮想ネットワーク内のトラフィックの発信元アドレスは、ポッド IP です。 仮想ネットワークの外部にあるトラフィックは、ノードのプライマリ IP に引き続き NAT 処理されます。

ノードでは、[Azure CNI][cni-networking] Kubernetes プラグインを使用します。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図][advanced-networking-diagram]

詳細については、[AKS クラスター用の Azure CNI の構成][aks-configure-advanced-networking]に関するページを参照してください。

### <a name="compare-network-models"></a>ネットワーク モデルを比較する

kubenet と Azure CNIは、両方とも AKS クラスターのネットワーク接続を提供します。 ただし、それぞれに長所と短所があります。 大まかに言えば、次の考慮事項が適用されます。

* **kubenet**
    * IP アドレス空間を節約します。
    * クラスターの外部からのポッドに到達するには、Kubernetes の内部または外部ロード バランサーを使用します。
    * ユーザー定義のルート (UDR) は、手動で管理および保守します。
    * クラスターあたり最大 400 ノード。
* **Azure CNI**
    * ポッドは完全な仮想ネットワーク接続を取得するため、接続されているネットワークからプライベート IP アドレスを介して直接アクセスできます。
    * より多くの IP アドレス空間を必要とします。

kubenet と Azure CNI には、次のような動作の違いが存在します。

| 機能                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 既存または新規の仮想ネットワークにクラスターをデプロイする                                            | サポートされています - UDR は手動で適用されます | サポートされています |
| ポッド間接続                                                                         | サポートされています | サポートされています |
| ポッドと VM の接続。同一仮想ネットワーク内の VM                                          | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| ポッドと VM の接続。ピアリングされた仮想ネットワーク内の VM                                            | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| VPN または Express Route を使用したオンプレミスへのアクセス                                                | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| サービス エンドポイントによって保護されているリソースへのアクセス                                             | サポートされています | サポートされています |
| ロード バランサー サービス、App Gateway、またはイングレス コントローラーを使用して、Kubernetes サービスを公開する | サポートされています | サポートされています |
| 既定の Azure DNS およびプライベート ゾーン                                                          | サポートされています | サポートされています |

DNS については、kubernet と Azure CNI のどちらのプラグインでも、CoreDNS (AKS で実行されるデプロイ) によって、その独自の自動スケーリングで DNS が提供されます。 Kubernetes の CoreDNS の詳細については、[DNS サービスのカスタマイズ](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)に関するページを参照してください。 CoreDNS は、既定では、不明なドメインを AKS クラスターがデプロイされている Azure Virtual Network の DNS 機能に転送するように構成されています。 そのため、Azure DNS およびプライベート ゾーンは、AKS で実行されるポッドに対して機能します。

### <a name="support-scope-between-network-models"></a>ネットワーク モデル間のサポート範囲

kubenet と Azure CNI はいずれも、使用するネットワーク モデルに関係なく次のいずれかの方法でデプロイすることができます。

* Azure プラットフォームは、AKS クラスターを作成したときに自動的に仮想ネットワーク リソースを作成して構成することができます。
* 仮想ネットワーク リソースを手動で作成して構成し、AKS クラスターを作成するときにそれらのリソースにアタッチすることができます。

サービス エンドポイントや UDR のような機能は kubenet と Azure CNI の両方でサポートされていますが、[AKS のサポート ポリシー][support-policies]は、どのような変更を行うことができるかを定義します。 次に例を示します。

* AKS クラスターの仮想ネットワーク リソースを手動で作成する場合は、独自の UDR またはサービス エンドポイントを構成するときにサポートされます。
* Azure プラットフォームで AKS クラスター用の仮想ネットワーク リソースを自動的に作成する場合、それらの AKS 管理対象リソースを手動で変更して独自の UDR またはサービスエンドポイントを構成することはできません。

## <a name="ingress-controllers"></a>イングレス コントローラー

LoadBalancer 型のサービスを作成すると、基になる Azure ロード バランサー リソースも作成されます。 ロード バランサーは、特定のポートでサービス内のポッドにトラフィックを分散するように構成されます。 

LoadBalancer は、レイヤー 4 でのみ動作します。 レイヤー 4 では、サービスは実際のアプリケーションを認識せず、追加のルーティングを考慮することはできません。

"*イングレス コントローラー*" はレイヤー 7 で動作し、インテリジェントなルールを使用してアプリケーションのトラフィックを分散させることができます。 イングレス コントローラーは通常、受信 URL に基づいて HTTP トラフィックを別のアプリケーションにルーティングします。

![AKS クラスターでのイングレス トラフィック フローを示す図][aks-ingress]

### <a name="create-an-ingress-resource"></a>イングレス リソースを作成する
AKS では、NGINX、同様のツール、または AKS の HTTP アプリケーション ルーティング機能を使用してイングレス リソースを作成します。 AKS クラスターで HTTP アプリケーションのルーティングを有効にすると、Azure プラットフォームがイングレス コントローラーと *External-DNS* コントローラーを作成します。 新しいイングレス リソースが Kubernetes に作成されると、必要な DNS A レコードがクラスター固有のDNS ゾーンに作成されます。 

詳細については、[HTTP アプリケーション ルーティングのデプロイ][aks-http-routing]に関する記事を参照してください。

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway イングレス コントローラー (AGIC)

Application Gateway イングレス コントローラー (AGIC) アドオンを使用すると、AKS のお客様は、Azure のネイティブ Application Gateway レベル 7 ロード バランサーを活用してクラウド ソフトウェアをインターネットに公開できます。 AGIC では、ホスト Kubernetes クラスターを監視し、Application Gateway を継続的に更新して、選択されたサービスをインターネットに公開します。 

AKS 用の AGIC アドオンの詳細については、「[Application Gateway イングレス コントローラーとは][agic-overview]」を参照してください。

### <a name="ssltls-termination"></a>SSL/TLS 終端

SSL/TLS 終端は、イングレスのもう 1 つの一般的な機能です。 HTTPS 経由でアクセスされる大規模な Web アプリケーションでは、アプリケーション自体の内部ではなく、イングレス リソースによって TLS 終端が処理されます。 TLS 証明書の自動生成と構成を提供することで、"Let's Encrypt" などのプロバイダーを使用するイングレス リソースを構成できます。 

Let's Encrypt を使用した NGINX イングレス コントローラーの構成の詳細については、[イングレスと TLS][aks-ingress-tls] に関する記事を参照してください。

### <a name="client-source-ip-preservation"></a>クライアント ソース IP の保持

クライアント ソース IP を AKS クラスター内のコンテナーへの要求上で保持するようにイングレス コントローラーを構成します。 イングレス コントローラーによりクライアントの要求が AKS クラスター内のコンテナーにルーティングされるときに、その要求の元のソース IP は、ターゲット コンテナーでは利用できません。 *クライアント ソース IP の保持* を有効にすると、クライアントに対するソース IP は、*X-Forwarded-For* 下にある要求ヘッダー内で利用できます。 

クライアント ソース IP の保持機能をイングレス コントローラー上で使用している場合は、TLS パススルーを使用できません。 クライアント ソース IP の保持と TLS パススルーは、*LoadBalancer* 型など、他のサービスによって使用できます。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループは、AKS ノードなどの VM のトラフィックをフィルター処理します。 LoadBalancer などのサービスを作成すると、必要なネットワーク セキュリティ グループ規則が Azure プラットフォームによって自動的に構成されます。 

AKS クラスター内のポッドに対するトラフィックをフィルター処理するネットワーク セキュリティ グループ規則を手動で構成する必要はありません。 Kubernetes サービス マニフェストの一部として、必要なポートと転送を定義するだけです。 Azure プラットフォームにより適切な規則が作成または更新されるようにします。 

ネットワーク ポリシーを使用して、ポッドにトラフィックのフィルター規則を自動的に適用することもできます。

## <a name="network-policies"></a>ネットワーク ポリシー

既定では、AKS クラスター内のすべてのポッドは制限なしにトラフィックを送受信できます。 セキュリティ向上のために、次のような、トラフィック フローを制御する規則を定義してください。
* バックエンド アプリケーションは、必要なフロントエンド サービスにのみ公開される。 
* データベース コンポーネントは、そこに接続するアプリケーション層からのみアクセスできる。

ネットワーク ポリシーは、ポッド間のトラフィック フローを制御できる、AKS で使用可能な Kubernetes の機能です。 割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、ポッドへのトラフィックを許可または拒否します。 ネットワーク セキュリティ グループが AKS ノードに適している一方で、ネットワーク ポリシーは、ポッドのトラフィック フローを制御するのにより適した、クラウド ネイティブな方法です。 AKS クラスター内でポッドが動的に作成されたときに、必要なネットワーク ポリシーを自動的に適用できます。

詳細については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][use-network-policies]」を参照してください。

## <a name="next-steps"></a>次のステップ

AKS ネットワークの使用を開始するために、[kubenet][aks-configure-kubenet-networking] または [Azure CNI][aks-configure-advanced-networking] を使用して、独自の IP アドレス範囲で AKS クラスターを作成および構成します。

関連付けられているベスト プラクティスについては、[AKS でのネットワーク接続とセキュリティに関するベスト プラクティス][operator-best-practices-network]に関するページを参照してください。

Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

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
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
