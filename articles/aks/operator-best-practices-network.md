---
title: ネットワーク リソースのベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) での仮想ネットワーク リソースと接続に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104951"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのネットワーク接続とセキュリティに関するベスト プラクティス

Azure Kubernetes Service (AKS) のクラスターを作成および管理する際には、ノードおよびアプリケーションに対するネットワーク接続を提供します。 これらのネットワーク リソースには、IP アドレス範囲、ロード バランサー、およびイングレス コントローラーが含まれます。 アプリケーションの高品質サービスを維持するには、これらのリソースを戦略化して構成する必要があります。

このベスト プラクティスの記事では、クラスター オペレーター向けのネットワーク接続とセキュリティに焦点を当てます。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * AKS の kubenet と Azure Container Networking Interface (CNI) のネットワーク モードを比較します。
> * 必要な IP アドレス指定と接続を計画します。
> * ロード バランサー、イングレス コントローラー、または Web アプリケーション ファイアウォール (WAF) を使用してトラフィックを分散します。
> * クラスター ノードへ安全に接続します。

## <a name="choose-the-appropriate-network-model"></a>適切なネットワーク モデルを選択する

> **ベスト プラクティスのガイダンス** 
> 
> AKS で Azure CNI ネットワークを使用すると、既存の仮想ネットワークまたはオンプレミスのネットワークと統合できます。 このネットワーク モデルでは、エンタープライズ環境でのリソースとコントロールをさらに分離することもできます。

仮想ネットワークは、AKS ノードおよび顧客がアプリケーションにアクセスするための基本的な接続を提供します。 仮想ネットワークに AKS クラスターをデプロイするには、次の 2 つの異なる方法があります。

* **Azure CNI ネットワーク**

    仮想ネットワークにデプロイし、[Azure CNI][cni-networking] Kubernetes プラグインを使用します。 ポッドは、他のネットワーク サービスまたはオンプレミス リソースにルーティングできる個々の IP を受け取ります。
* **Kubenet ネットワーク**

    Azure は、クラスターがデプロイされて[ kubenet][kubenet]　Kubernetes プラグインを使用する際に、仮想ネットワーク リソースを管理します。


運用環境のデプロイでは、kubenet と Azure CNI の両方が有効なオプションです。

### <a name="cni-networking"></a>CNI ネットワーク

Azure CNI は、コンテナー ランタイムがネットワーク プロバイダーに要求を行えるようにする、ベンダー中立のプロトコルです。 これはポッドとノードに IP アドレスを割り当て、既存の Azure 仮想ネットワークに接続する際に IP アドレス管理 (IPAM) 機能を提供します。 各ノードとポッド リソースは、Azure 仮想ネットワークの IP アドレスを受け取ります。他のリソースやサービスと通信するための追加のルーティングは必要ありません。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図](media/operator-best-practices-network/advanced-networking-diagram.png)

特に、運用向け Azure CNI ネットワークでは、リソースの制御と管理を分離できます。 セキュリティの観点から、多くの場合に、それらのリソースを異なるチームが管理し、セキュリティで保護することが望まれます。 Azure CNI ネットワークを使用すると、各ポッドに割り当てられた IP アドレスを介して、既存の Azure リソース、オンプレミスのリソース、またはその他のサービスに直接接続できます。

Azure CNI ネットワークを使用する場合、仮想ネットワーク リソースは AKS クラスターとは別個のリソース グループに含まれます。 これらのリソースにアクセスして管理するためのアクセス許可を AKS クラスター ID に委任します。 AKS クラスターで使用されるクラスター ID には、少なくとも、ご利用の仮想ネットワーク内のサブネットに対する[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md#network-contributor)のクセス許可が必要です。 

組み込みのネットワークの共同作成者ロールを使用する代わりに、[カスタム ロール](../role-based-access-control/custom-roles.md)を定義する場合は、次のアクセス許可が必要です。
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

既定では、AKS はクラスター ID にマネージド ID を使用します。 ただし、代わりにサービス プリンシパルを使用できます。 詳細情報は、次のとおりです。
* AKS サービス プリンシパルの委任については、「[他の Azure リソースへのアクセスを委任する][sp-delegation]」を参照してください。 
* マネージド ID については、[マネージド ID](use-managed-identity.md) の使用に関するページを参照してください。

各ノードとポッドは独自の IP アドレスを受け取るため、AKS サブネットのアドレス範囲を計画します。 次の点に留意してください。
* サブネットは、デプロイするすべてのノード、ポッド、およびネットワーク リソースの IP アドレスを提供するのに十分な大きさである必要があります。 
    * kubenet と Azure CNI ネットワークの両方で実行する各ノードには、ポッドの数に対してデフォルトの制限があります。
* 各 AKS クラスターは、その独自のサブネットに配置する必要があります。 
* 既存のネットワーク リソースと重複する IP アドレス範囲は使用しないでください。 
    * Azure でオンプレミスまたはピア接続されたネットワークへの接続を許可する上で必要です。
* スケールアウト イベントまたはクラスター アップグレードを処理するには、割り当てられたサブネットで使用できる追加の IP アドレスが必要です。 
    * Windows Server コンテナーを使用している場合、この追加のアドレス空間は特に重要です。これらのノード プールをアップグレードして最新のセキュリティ パッチを適用する必要があるためです。 Windows Server ノードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関する記事を参照してください。

必要な IP アドレスを計算するには、[AKS での Azure CNI ネットワークの構成][advanced-networking]に関するページを参照してください。

Azure CNI ネットワークを使用してクラスターを作成する場合は、Docker ブリッジ アドレス、DNS サービス IP、サービス アドレス範囲など、クラスターで使用する他のアドレス範囲を指定します。 一般的には、これらのアドレス範囲について以下を確認してください。
* 互いに重複させないでください。
* 仮想ネットワーク、サブネット、オンプレミス、ピア接続されたネットワークなど、クラスターに関連付けられているネットワークと重複さないでください。 

これらのアドレス範囲の制限とサイズ設定の詳細については、[AKS での Azure CNI ネットワークの構成][advanced-networking]に関するページを参照してください。

### <a name="kubenet-networking"></a>Kubenet ネットワーク

kubenet では、クラスターをデプロイする前に仮想ネットワークを設定する必要はありませんが、待機には次のような欠点があります。

* ノードとポッドは異なる IP サブネットに配置されるため、ユーザー定義ルーティング (UDR) と IP 転送はポッドとノード間のトラフィックをルーティングします。 この追加のルーティングにより、ネットワーク パフォーマンスが低下する場合があります。
* 既存のオンプレミスのネットワークへの接続または他の Azure 仮想ネットワークへのピアリングが複雑である可能性があります。

仮想ネットワークとサブネットは AKS クラスターとは別に作成されないため、Kubernet は次のような場合に最適です。
* 小規模な開発またはテストのワークロード。 
* トラフィックが少ない単純な Web サイト。
* ワークロードのコンテナーへのリフト アンド シフト。

ほとんどの運用環境デプロイでは、Azure CNI ネットワークを計画して使用する必要があります。

[kubenet を使用して独自の IP アドレス範囲と仮想ネットワークを構成][aks-configure-kubenet-networking]することもできます。 Azure CNI ネットワークと同様に、これらのアドレス範囲を互いに重複させることはできません。また、クラスターに関連付けられているネットワーク (仮想ネットワーク、サブネット、オンプレミス、ピアリングされたネットワークなど) と重複させることもできません。 

これらのアドレス範囲の制限とサイズ設定の詳細については、[AKS の独自の IP アドレス範囲での kubenet ネットワークの使用][aks-configure-kubenet-networking]に関するページを参照してください。

## <a name="distribute-ingress-traffic"></a>イングレス トラフィックを分散する

> **ベスト プラクティスのガイダンス** 
> 
> HTTP または HTTPS トラフィックをアプリケーションに分散するには、イングレス リソースとコントローラーを使用します。 Azure ロード バランサーと比較すると、イングレス コントローラーでは追加機能が提供されており、ネイティブの Kubernetes リソースとして管理できます。

Azure ロード バランサーを使用すると顧客のトラフィックを AKS クラスター内のアプリケーションに分散できますが、そのトラフィックを理解するには限界があります。 ロード バランサー リソースはレイヤー 4 で動作し、プロトコルまたはポートに基づいてトラフィックを分散します。 

HTTP または HTTPS を使用するほとんどの Web アプリケーションでは、レイヤー 7 で動作する Kubernetes イングレス リソースとコントローラーを使用する必要があります。 イングレスは、アプリケーションの URL に基づいてトラフィックを分散し、TLS/SSL ターミネーションを処理することができます。 イングレスでは、公開してマップする IP アドレスの数も減少します。 

ロード バランサーでは、通常、各アプリケーションのパブリック IP アドレスが AKS クラスター内のサービスに割り当てられてマップされている必要があります。 イングレス リソースでは、単一の IP アドレスが複数のアプリケーションにトラフィックを分散できます。

![AKS クラスターでのイングレス トラフィック フローを示す図](media/operator-best-practices-network/aks-ingress.png)

 イングレスには、次の 2 つのコンポーネントがあります。

 * イングレス *リソース*
 * イングレス *コントローラー*

### <a name="ingress-resource"></a>イングレス リソース

*イングレス リソース* は、`kind: Ingress` の YAML マニフェストです。 これは、AKS クラスターで実行するサービスに対してトラフィックをルーティングするためのホスト、証明書、およびルールを定義します。 

次の YAML マニフェストの例は、*myapp.com* のトラフィックを *blogservice* と *storeservice* の 2 つのサービスのどちらかに分散します。 顧客は、アクセスする URL に基づいてどちらかのサービスに転送されます。

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

### <a name="ingress-controller"></a>イングレス コントローラー

*イングレス コントローラー* は AKS ノードで実行され、着信要求を監視するデーモンです。 その後、トラフィックは、イングレス リソースで定義されたルールに基づいて分散されます。 最も一般的なイングレス コントローラーは [NGINX] に基づいていますが、AKS では特定のコントローラーに制限していません。 [Contour][contour]、[HAProxy][haproxy]、[Traefik][traefik] などを使用できます。

イングレス コントローラーは Linux ノード上でスケジュールする必要があります。 YAML マニフェストまたは Helm グラフのデプロイでノード セレクターを使用して、リソースが Linux ベースのノード上で実行されるように指定します。 詳細については、[ノード セレクターを使用して AKS でポッドをスケジュールする場所を制御する][concepts-node-selectors]方法に関する記事を参照してください。

> [!NOTE]
> Windows Server ノードでは、イングレス コントローラーを実行しないでください。

イングレスには、次の操作方法に関するガイドを含め、さまざまなシナリオがあります。

* [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
* [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
* [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
* Let's Encrypt を使用して、[動的パブリック IP アドレスを指定][aks-ingress-tls]または[静的パブリック IP アドレスを指定][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Web アプリケーション ファイアウォール (WAF) を使用してトラフィックをセキュリティで保護する

> **ベスト プラクティスのガイダンス**
> 
> 着信トラフィックをスキャンして潜在的な攻撃を検出するには、[Barracuda WAF for Azure][barracuda-waf] や Azure Application Gateway などの Web アプリケーション ファイアウォール (WAF) を使用します。 また、より高度なこれらのネットワーク リソースは、単なる HTTP 接続や HTTPS 接続または基本的な TLS ターミネーション以外でもトラフィックをルーティングできます。

通常、イングレス コントローラーは、トラフィックをサービスやアプリケーションに分散する、AKS クラスター内の Kubernetes リソースです。 このコントローラーは AKS ノード上でデーモンとして実行され、CPU、メモリ、ネットワーク帯域幅など、ノードのリソースの一部を消費します。 大規模な環境では、次のことを行う必要があります。
* このトラフィックのルーティングまたは TLS 終端の一部を、AKS クラスター外のネットワーク リソースにオフロードします。
* 着信トラフィックをスキャンして潜在的な攻撃を検出します。

![Azure App Gateway などの Web アプリケーション ファイアウォール (WAF) は、AKS クラスターのトラフィックを保護および分散することができます](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

この追加のセキュリティ層では、Web アプリケーション ファイアウォール (WAF) によって着信トラフィックのフィルター処理が行われます。 Open Web Application Security Project (OWASP) では、一連のルールを使用して、クロスサイト スクリプティングや Cookie ポイズニングなどの攻撃を監視します。 [Azure Application Gateway][app-gateway] (現在 AKS でプレビュー中) は AKS クラスターと統合する WAF で、トラフィックが AKS クラスターとアプリケーションに到達する前にこれらのセキュリティ機能をロックします。 

これらの機能は他のサード パーティ製ソリューションでも実行されるため、既存の投資や専門知識を希望する製品で引き続き使用できます。

ロード バランサーまたはイングレス リソースは、AKS クラスターで継続的に実行され、トラフィックの分散を調整します。 App Gateway は、リソース定義でイングレス コントローラーとして一元的に管理できます。 最初に、[Application Gateway イングレス コントローラーを作成します][app-gateway-ingress]。

## <a name="control-traffic-flow-with-network-policies"></a>ネットワーク ポリシーを使用してトラフィック フローを制御する

> **ベスト プラクティスのガイダンス** 
>
> ネットワーク ポリシーを使用して、ポッドへのトラフィックを許可または拒否します。 既定では、1 つのクラスター内でポッド間のすべてのトラフィックが許可されます。 セキュリティを強化するには、ポッドの通信を制限するルールを定義します。

ネットワーク ポリシーは、ポッド間のトラフィック フローを制御できる、AKS で使用可能な Kubernetes の機能です。 ユーザーは、割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、トラフィックを許可または拒否します。 トラフィックのフローを制御するには、ネットワーク ポリシーを使用するのがクラウド ネイティブな方法です。 ポッドは AKS クラスター内で動的に作成されるため、必要なネットワーク ポリシーを自動的に適用できます。

ネットワーク ポリシーを使用するには、新しい AKS クラスターの作成時にこの機能を有効にします。 既存の AKS クラスターでネットワーク ポリシーを有効にすることはできません。 必要なクラスターでネットワーク ポリシーを有効にすることを事前に計画してください。 

>[!NOTE]
>ネットワーク ポリシーは、AKS の Linux ベースのノードとポッドに対してのみ使用する必要があります。

ネットワーク ポリシーは、YAML マニフェストを使用して Kubernetes リソースとして作成します。 ポリシーは、トラフィック フローを定義するイングレス ルールまたはエグレス ルールを使用して、定義されたポッドに適用されます。 

次の例では、*app: backend* ラベルが適用されたポッドにネットワーク ポリシーを適用します。 イングレス ルールでは、*app: frontend* ラベルのあるポッドからのトラフィックのみを許可します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

ポリシーの概要については、「[SAzure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][use-network-policies]」を参照してください。

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>踏み台ホストを介してノードに安全に接続する

> **ベスト プラクティスのガイダンス** 
>
> AKS ノードへのリモート接続は公開しないでください。 管理仮想ネットワーク内に踏み台ホスト (jump box) を作成します。 踏み台ホストを使用すると、AKS クラスターへのトラフィックをリモート管理タスクに安全にルーティングできます。

AKS のほとんどの操作は、Azure 管理ツールを使用するか、Kubernetes API サーバー経由で完了できます。 AKS ノードはプライベート ネットワークでのみ使用でき、パブリック インターネットには接続されません。 ノードに接続してメンテナンスとサポートを提供するには、bastion ホストまたはジャンプ ボックスを介して接続をルーティングします。 このホストが、AKS クラスターの仮想ネットワークとは別の安全にピアリングされた管理仮想ネットワークに存在することを確認します。

![踏み台ホスト (jump box) を使用して AKS ノードに接続する](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

踏み台ホストの管理ネットワークは、セキュリティで保護する必要もあります。 [Azure ExpressRoute][expressroute] または [VPN Gateway][vpn-gateway] を使用してオンプレミス ネットワークに接続し、ネットワーク セキュリティ グループを使用してアクセスを制御します。

## <a name="next-steps"></a>次のステップ

この記事では、ネットワーク接続およびセキュリティに焦点を当てました。 Kubernetes におけるネットワークの基礎の詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念][aks-concepts-network]」を参照してください。

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool