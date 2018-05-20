---
title: Azure Kubernetes Service (AKS) のネットワーク構成
description: Azure Kubernetes Service (AKS) の基本的なネットワーク構成と高度なネットワーク構成について説明します。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 80d12d1f5d6b388c46ed90eb84b7bc00250e17ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のネットワーク構成

Azure Kubernetes Service (AKS) クラスターを作成する場合、**[基本]** または **[高度]** という 2 つのネットワーク オプションから選択できます。

## <a name="basic-networking"></a>基本ネットワーク

**基本**ネットワーク オプションは、AKS クラスター作成の既定の構成です。 クラスターとそのポッドのネットワーク構成は Azure によって完全に管理されるので、カスタムの VNet 構成が必要ではない展開に適しています。 [基本] ネットワークを選択する場合、クラスターに割り当てるサブネットや IP アドレスの範囲など、ネットワーク構成を制御することはできません。

[基本] ネットワーク用に構成された AKS クラスターのノードは、[kubenet][kubenet] Kubernetes プラグインを使用します。

## <a name="advanced-networking"></a>[高度] ネットワーク

**[高度]** ネットワークは、構成した Azure Virtual Network (VNet) にポッドを配置し、VNet リソースに自動接続されます。また、VNets が提供する豊富な機能セットと統合されています。
現在、AKS クラスターを [Azure Portal][portal] に展開する場合、または Resource Manager テンプレートを使用する場合にのみ [高度] ネットワークを使用できます。

[高度] ネットワーク用に構成された AKS クラスターのノードは、[Azure Container Networking Interface (CNI)][cni-networking] Kubernetes プラグインを使用します。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>[高度] ネットワークの機能

[高度] ネットワークには次の利点があります。

* AKS クラスターを既存の VNet に展開するか、クラスター用に新しい VNet とサブネットを作成します。
* クラスター内の各ポッドには VNet の IP アドレスが割り当てられ、クラスター内の他のポッドや VNet 内の他の VM と直接通信できます。
* ポッドは、ExpressRoute やサイト間 (S2S) VPN 接続経由で、ピアリングされた VNet 内の他のサービスやオンプレミス ネットワークと接続することができます。 ポッドにはオンプレミスからも到達できます。
* Azure Load Balancer を使用して、Kubernetes サービスを外部または内部に公開できます。 これは [基本] ネットワークの機能でもあります。
* サービス エンドポイントが有効なサブネット内のポッドは、Azure サービス (Azure Storage や SQL DB など) に安全に接続できます。
* ユーザー定義のルート (UDR) を使用して、ポッドのトラフィックをネットワーク仮想アプライアンスにルーティングできます。
* ポッドはパブリック インターネット上のリソースにアクセスできます。 これは [基本] ネットワークの機能でもあります。

> [!IMPORTANT]
> [高度] ネットワーク用に構成された AKS クラスター内の各ノードは、最大 **30 個のポッド**をホストできます。 Azure CNI プラグインと共に使用するようにプロビジョニングされた各 VNet は、**4,096 個の IP アドレス** (/20) に制限されています。

## <a name="configure-advanced-networking"></a>[高度] ネットワークを構成する

Azure Portal で [AKS クラスターを作成](kubernetes-walkthrough-portal.md)するときに、[高度] ネットワーク用に以下のパラメーターを構成できます。

**[仮想ネットワーク]**: Kubernetes クラスターを展開する VNet。 クラスターに新しい VNet を作成する場合は、*[新規作成]* を選択し、*[仮想ネットワークの作成]* セクションの手順に従います。

**[サブネット]**: クラスターを展開する VNet 内のサブネット。 クラスターの VNet に新しいサブネットを作成する場合は、*[新規作成]* を選択し、*[サブネットの作成]* セクションの手順に従います。

**[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)**: Kubernetes クラスターのサービス IP の IP アドレス範囲。 クラスターの VNet の IP アドレス範囲に含まれる範囲を指定することはできません。

**[Kubernetes DNS service IP address]\(Kubernetes DNS サービスの IP アドレス\)**: クラスターの DNS サービスの IP アドレス。 *[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)* 内に含まれるアドレスを指定する必要があります。

**[Docker Bridge address]\(Docker ブリッジのアドレス\)**: Docker ブリッジに割り当てる IP アドレスとネットマスク。 クラスターの VNet の IP アドレス範囲に含まれる IP アドレスを指定することはできません。

Azure Portal の次のスクリーン ショットは、AKS クラスターの作成時にこれらの設定を構成する場合の例を示しています。

![Azure Portal の [高度] ネットワーク構成][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>クラスターの IP アドレス指定を計画する

[高度] ネットワークを使用して構成されたクラスターには、追加の計画が必要です。 VNet とそのサブネットのサイズは、クラスター内で同時に実行する予定のポッドの数と、スケーリング要件に応じて調整する必要があります。

ポッドとクラスターのノードの IP アドレスは、VNet 内の指定されたサブネットから割り当てられます。 各ノードは、プライマリ IP (ノード自体の IP) と、Azure CNI によって事前に構成された 30 個の追加 IP アドレス (ノードに対してスケジュールされているポッドに割り当てられたアドレス) を使用して構成されます。 クラスターをスケールアウトすると、サブネットの IP アドレスを使用して各ノードが同様に構成されます。

前述のように、Azure CNI プラグインと共に使用するようにプロビジョニングされた各 VNet は、**4,096 個の IP アドレス** (/20) に制限されています。 [高度] ネットワーク用に構成されたクラスター内の各ノードは、最大 **30 個のポッド**をホストできます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答は、**[高度]** ネットワーク構成に適用されます。

* *Azure CLI で[高度] ネットワークを構成できますか。*

  いいえ。 現在、AKS クラスターを Azure Portal に展開する場合、または Resource Manager テンプレートを使用する場合にのみ [高度] ネットワークを使用できます。

* *クラスター サブネットに VM を展開できますか。*

  いいえ。 Kubernetes クラスターで使用されているサブネットに VM を展開することはできません。 VM を同じ VNet に展開することはできますが、異なるサブネットに展開する必要があります。

* *ポッドごとのネットワーク ポリシーを構成できますか。

  いいえ。 ポッドごとのネットワーク ポリシーは現在サポートされていません。

* *ノードに展開できるポッドの最大数は構成できますか。*

  既定では、各ノードは最大 30 個のポッドをホストできます。 現在のところ、最大値を変更するには、Resource Manager テンプレートを使用してクラスターを展開するときに `maxPods` プロパティを変更する必要があります。

* *AKS クラスターの作成時に作成したサブネットの追加のプロパティを構成するにはどうすればよいですか。たとえば、サービス エンドポイントなどのプロパティです。*

  AKS クラスターの作成中時に作成する VNet とサブネットのプロパティの詳細な一覧は、Azure Portal の標準の VNet 構成ページで構成できます。

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
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
