---
title: Azure Kubernetes Service (AKS) のネットワーク構成
description: Azure Kubernetes Service (AKS) の基本的なネットワーク構成と高度なネットワーク構成について説明します。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: marsma
ms.openlocfilehash: 051402a319e1dc26145b5a1602a4caeffa7fba19
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42445509"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のネットワーク構成

Azure Kubernetes Service (AKS) クラスターを作成する場合、**[基本]** または **[高度]** という 2 つのネットワーク オプションから選択できます。

## <a name="basic-networking"></a>基本ネットワーク

**基本**ネットワーク オプションは、AKS クラスター作成の既定の構成です。 クラスターとそのポッドのネットワーク構成は Azure によって完全に管理されるので、カスタムの VNet 構成が必要ではない展開に適しています。 [基本] ネットワークを選択する場合、クラスターに割り当てるサブネットや IP アドレスの範囲など、ネットワーク構成を制御することはできません。

[基本] ネットワーク用に構成された AKS クラスターのノードは、[kubenet][kubenet] Kubernetes プラグインを使用します。

## <a name="advanced-networking"></a>[高度] ネットワーク


  **[高度]** ネットワークは、構成した Azure Virtual Network (VNet) にポッドを配置し、VNet リソースに自動接続されます。また、VNets が提供する豊富な機能セットと統合されています。 [Azure portal][portal]、Azure CLI、または Resource Manager テンプレートを使って AKS クラスターを展開するときは、[高度] ネットワークを使用できます。

[高度] ネットワーク用に構成された AKS クラスターのノードは、[Azure Container Networking Interface (CNI)][cni-networking] Kubernetes プラグインを使用します。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>[高度] ネットワークの機能

[高度] ネットワークには次の利点があります。

* AKS クラスターを既存の VNet に展開するか、クラスター用に新しい VNet とサブネットを作成します。
* クラスター内の各ポッドには VNet の IP アドレスが割り当てられ、クラスター内の他のポッドや VNet 内の他のノードと直接通信できます。
* ポッドは、ExpressRoute やサイト間 (S2S) VPN 接続経由で、ピアリングされた VNet 内の他のサービスやオンプレミス ネットワークと接続することができます。 ポッドにはオンプレミスからも到達できます。
* Azure Load Balancer を使用して、Kubernetes サービスを外部または内部に公開できます。 これは [基本] ネットワークの機能でもあります。
* サービス エンドポイントが有効なサブネット内のポッドは、Azure サービス (Azure Storage や SQL DB など) に安全に接続できます。
* ユーザー定義のルート (UDR) を使用して、ポッドのトラフィックをネットワーク仮想アプライアンスにルーティングできます。
* ポッドはパブリック インターネット上のリソースにアクセスできます。 これは [基本] ネットワークの機能でもあります。

## <a name="advanced-networking-prerequisites"></a>[高度] ネットワークの前提条件

* AKS クラスターの VNet では、送信インターネット接続を許可する必要があります。
* 同じサブネット内に複数の AKS クラスターを作成しないでください。
* AKS クラスターでは、Kubernetes サービスのアドレス範囲に `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16` は使用できません。
* AKS クラスターで使用されるサービス プリンシパルには、少なくとも、ご利用の VNet 内のサブネットに対する[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md#network-contributor)アクセス許可が必要です。 組み込みのネットワークの共同作成者ロールを使用する代わりに、[カスタム ロール](../role-based-access-control/custom-roles.md)を定義する場合は、次のアクセス許可が必要です。
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>クラスターの IP アドレス指定を計画する

[高度] ネットワークを使用して構成されたクラスターには、追加の計画が必要です。 VNet とそのサブネットのサイズは、実行する予定のポッドの数とクラスターのノードの数の両方に対応できる必要があります。

ポッドとクラスターのノードの IP アドレスは、VNet 内の指定されたサブネットから割り当てられます。 各ノードは、プライマリ IP (ノードの IP) と、Azure CNI によって事前に構成された 30 個の追加の IP アドレス (ノードに対してスケジュールされているポッドに割り当てられたアドレス) を使用して構成されます。 クラスターをスケールアウトすると、サブネットの IP アドレスを使用して各ノードが同様に構成されます。

AKS クラスターの IP アドレス計画は、VNet、ノードとポッドの 1 つ以上のサブネット、Kubernetes サービスのアドレス範囲で構成されます。

| アドレス範囲/Azure リソース | 制限とサイズ変更 |
| --------- | ------------- |
| 仮想ネットワーク | Azure VNet は最大 /8 ですが、16,000 個の構成済み IP アドレスに制限されています。 |
| サブネット | クラスターにプロビジョニングされている可能性のあるノード、ポッド、すべての Kubernetes、および Azure のリソースを収容するのに十分な大きさである必要があります。 たとえば、内部に Azure Load Balancer をデプロイする場合は、そのフロントエンド IP は、パブリック IP ではなく、クラスター サブネットから割り当てられています。 <p/>サブネットの*最小*サイズの計算式は、`(number of nodes) + (number of nodes * pods per node)` です。 <p/>たとえば、50 のノードから構成されるクラスターは、`(50) + (50 * 30) = 1,550` (/21 以上) です。 |
| Kubernetes サービスのアドレス範囲 | この範囲は、この VNet 上のネットワーク要素、またはこの VNet に接続されているネットワーク要素では使用しないでください。 サービスのアドレスの CIDR は、/12 より小さくする必要があります。 |
| Kubernetes DNS サービスの IP アドレス | クラスター サービス検索 (kube-dns) で使用される、Kubernetes サービスのアドレス範囲内の IP アドレス。 |
| Docker ブリッジ アドレス | ノード上の Docker ブリッジの IP アドレスとして使用される IP アドレス(CIDR 表記)。 既定値は 172.17.0.1/16 です。 |

Azure CNI プラグインと共に使用するためにプロビジョニングされた各 VNet は、**16,000 個の構成済み IP アドレス**に制限されています。

## <a name="maximum-pods-per-node"></a>ノードごとの最大ポッド数

AKS クラスター内のノードごとの既定の最大ポッド数は、基本ネットワークおよび高度ネットワークと、クラスターのデプロイ方法によって異なります。

### <a name="default-maximum"></a>既定の最大数

* 基本ネットワーク: **ノードごとに 110 ポッド**
* 高度ネットワーク: **ノードごとに 30 ポッド**

### <a name="configure-maximum"></a>最大数の構成

デプロイの方法に応じて、AKS クラスター内のノードごとのポッドの最大数を変更することができます。

* **Azure CLI**: [az aks create][az-aks-create] コマンドを使用して、クラスターをデプロイするときに `--max-pods` 引数を指定します。
* **Resource Manager テンプレート**: Resource Manager テンプレートを使用してクラスターをデプロイするときに、[ManagedClusterAgentPoolProfile] オブジェクトに `maxPods` プロパティを指定します。
* **Azure Portal**: Azure Portal を使用してクラスターをデプロイするときに、ノードごとのポッドの最大数を変更することはできません。 Azure Portal にデプロイされた高度なネットワーク クラスターのポッド数は、ノードあたり 30 に制限されています。

## <a name="deployment-parameters"></a>展開のパラメーター

AKS クラスターを作成するときに、高度なネットワーク用に以下のパラメーターを構成できます。

**[仮想ネットワーク]**: Kubernetes クラスターを展開する VNet。 クラスターに新しい VNet を作成する場合は、*[新規作成]* を選択し、*[仮想ネットワークの作成]* セクションの手順に従います。 VNet に構成される IP アドレスは 16,000 に制限されています。

**[サブネット]**: クラスターを展開する VNet 内のサブネット。 クラスターの VNet に新しいサブネットを作成する場合は、*[新規作成]* を選択し、*[サブネットの作成]* セクションの手順に従います。

**Kubernetes サービスのアドレス範囲**: これは、Kubernetes によってクラスター内の[サービス][services]に割り当てられる仮想 IP のセットです。 次の要件を満たす任意のプライベート アドレス範囲を使用できます。

* クラスターの VNet の IP アドレス範囲に含まれていてはなりません。
* クラスター VNet とピアになっている他のどの Vnet とも重複していてはなりません
* オンプレミスのどの IP アドレスとも重複していてはなりません
* `169.254.0.0/16`、`172.30.0.0/16`、または `172.31.0.0/16` の範囲内にあってはなりません

技術的には、クラスターと同じ VNet 内のサービス アドレス範囲を指定できますが、お勧めはしません。 重複する IP アドレス範囲を使うと、予期しない動作になる可能性があります。 詳細については、この記事の [FAQ](#frequently-asked-questions) のセクションを参照してください。 Kubernetes サービスについて詳しくは、Kubernetes ドキュメントの「[Services][services]」(サービス) をご覧ください。

**[Kubernetes DNS service IP address]\(Kubernetes DNS サービスの IP アドレス\)**: クラスターの DNS サービスの IP アドレス。 *[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)* 内に含まれるアドレスを指定する必要があります。

**[Docker Bridge address]\(Docker ブリッジのアドレス\)**: Docker ブリッジに割り当てる IP アドレスとネットマスク。 クラスターの VNet の IP アドレス範囲に含まれる IP アドレスを指定することはできません。

## <a name="configure-networking---cli"></a>ネットワークを構成する - CLI

Azure CLI を使って AKS クラスターを作成するときも、高度なネットワークを構成できます。 高度なネットワーク機能を有効にして新しい AKS クラスターを作成するには、次のコマンドを使います。

最初に、AKS クラスターを参加させる既存のサブネットのサブネット リソース ID を取得します。

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

[az aks create][az-aks-create] コマンドに `--network-plugin azure` 引数を指定して実行し、高度なネットワークでクラスターを作成します。 前の手順で収集したサブネット ID で、`--vnet-subnet-id` の値を更新します。

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>ネットワークを構成する - ポータル

Azure Portal の次のスクリーン ショットは、AKS クラスターの作成時にこれらの設定を構成する場合の例を示しています。

![Azure Portal の [高度] ネットワーク構成][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答は、**[高度]** ネットワーク構成に適用されます。

* *クラスター サブネットに VM を展開できますか。*

  いいえ。 Kubernetes クラスターで使用されているサブネットに VM を展開することはできません。 VM を同じ VNet に展開することはできますが、異なるサブネットに展開する必要があります。

* *ポッドごとのネットワーク ポリシーを構成できますか。*

  いいえ。 ポッドごとのネットワーク ポリシーは現在サポートされていません。

* *ノードに展開できるポッドの最大数は構成できますか。*

  はい。Azure CLI または Resource Manager テンプレートを使用してクラスターをデプロイするときに構成することができます。 「[ノードごとの最大ポッド数](#maximum-pods-per-node)」を参照してください。

* *AKS クラスターの作成時に作成したサブネットの追加のプロパティを構成するにはどうすればよいですか。たとえば、サービス エンドポイントなどのプロパティです。*

  AKS クラスターの作成中時に作成する VNet とサブネットのプロパティの詳細な一覧は、Azure Portal の標準の VNet 構成ページで構成できます。

* **[Kubernetes サービスのアドレス範囲]** *のクラスター VNet 内で別のサブネットを使用できますか*。

  お勧めはしませんが、この構成は可能です。 サービスのアドレス範囲は、Kubernetes によってクラスター内のサービスに割り当てられる仮想 IP (VIP) のセットです。 Azure のネットワークには、Kubernetes クラスターのサービスの IP 範囲の可視性がありません。 クラスターのサービス アドレス範囲には可視性がないため、後でクラスター VNet にサービスのアドレス範囲と重複する新しいサブネットが作成される可能性があります。 このような重複が発生した場合、Kubernetes は、サブネット内の他のリソースによって既に使用されている IP をサービスに割り当てる可能性があり、予期しない動作やエラーの原因となります。 クラスターの VNet の外部のアドレス範囲を使用することで、この重複のリスクを回避できます。

## <a name="next-steps"></a>次の手順

### <a name="networking-in-aks"></a>AKS のネットワーク

AKS のネットワークの詳細については、次の記事を参照してください。

[Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する](static-ip.md)

[Azure Container Service (AKS) での HTTPS イングレス](ingress.md)

[Azure Container Service (AKS) で内部ロード バランサーを使用する](internal-lb.md)

### <a name="acs-engine"></a>ACS Engine

[Azure Container Service Engine (ACS Engine)][acs-engine] は、Azure に Docker 対応クラスターを展開する場合に使用できる Azure Resource Manager テンプレートを生成するオープンソース プロジェクトです。 Kubernetes、DC/OS、Swarm Mode、Swarm オーケストレーターは、ACS Engine を使用して展開できます。

ACS Engine で作成された Kubernetes クラスターは、[kubenet][kubenet] および [Azure CNI][cni-networking] プラグインの両方をサポートしています。 そのため、ACS Engine は、基本ネットワークと高度なネットワークの両方のシナリオをサポートしています。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
