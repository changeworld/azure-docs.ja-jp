---
title: AKS での事業継続とディザスター リカバリーに関するベスト プラクティス
description: Azure Kubernetes Services (AKS) でアプリケーションの最大アップタイムを達成して、高可用性を提供し、ディザスター リカバリーに備えるための、クラスター オペレーターのベスト プラクティスについて説明します。
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105087"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理するにあたっては、アプリケーションのアップタイムが重要になります。 既定で、AKS により [仮想マシン スケール セット (VMSS)](../virtual-machine-scale-sets/overview.md) 内の複数のノードを使用して高可用性が提供されます。 しかし、これらの複数のノードでは、お使いのシステムはリージョン障害から保護されません。 アップタイムを最大化するには、ビジネス継続性の維持とディザスター リカバリーの準備について事前に計画を立てておきます。

この記事では、AKS でのビジネス継続性とディザスター リカバリーに関する計画を立てる方法に重点を置いて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数リージョン内の AKS クラスターに関する計画を立てる。
> * Azure Traffic Manager を使用して、複数のクラスター間でトラフィックをルーティングする。
> * コンテナー イメージのレジストリに geo レプリケーションを使用する。
> * 複数のクラスター間でのアプリケーション状態に関する計画を立てる。
> * 複数のリージョン間でストレージをレプリケートする。

## <a name="plan-for-multiregion-deployment"></a>複数リージョンのデプロイに関する計画を立てる

> **ベスト プラクティス**
>
> AKS クラスターを複数デプロイする場合は、AKS を使用できるリージョンを選択します。 ペアになっているリージョンを使用します。

AKS クラスターは、1 つのリージョンにデプロイされます。 リージョンの障害からシステムを保護するには、複数のリージョンにまたがる複数の AKS クラスターにアプリケーションをデプロイします。 AKS クラスターをデプロイする場所を計画する際には、次の点を考慮してください。

* [**AKS リージョンの可用性**](./quotas-skus-regions.md#region-availability)
    * ユーザーに近いリージョンを選択しましょう。 
    * AKS では、継続的に新しいリージョンを展開しています。
* [**Azure ペア リージョン**](../best-practices-availability-paired-regions.md)
    * 利用する地理的な領域に対してペアになっている 2 つのリージョンを選択します。
    * ペアになったリージョンでは、プラットフォームの更新が調整され、必要に応じて復旧作業の優先順位が付けられます。
* **サービスの提供状況**
    * ペアになったリージョンを、ホット/ホット、ホット/ウォーム、またはホット/コールドのいずれにするかを決定します。
    * 両方のリージョンを同時に実行する場合、一方のリージョンをトラフィックの提供を開始する "*準備ができた*" 状態にするのか、 または、
    * 一方のリージョンに、トラフィックの提供を準備する時間を与えるのか

AKS リージョンの可用性と、ペアになったリージョンは、複合的に考慮する必要があります。 リージョンのディザスター リカバリーを連携して管理するように設計されたペアのリージョンに AKS クラスターをデプロイします。 たとえば、AKS は "米国東部" と "米国西部" で利用できます。 これらのリージョンはペアになっています。 AKS の BC/DR 戦略を作成するときは、これらの 2 つのリージョンを選択します。

アプリケーションをデプロイするときは、これらの複数の AKS クラスターにデプロイするためのもう 1 つのステップを CI/CD パイプラインに追加します。 デプロイ パイプラインを更新すると、アプリケーションがリージョンおよび AKS クラスターの 1 つだけにデプロイされるのを防ぐことができます。 このシナリオの場合、セカンダリ リージョンに送信された顧客のトラフィックは、最新のコード更新を受信しません。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager を使用してトラフィックをルーティングする

> **ベスト プラクティス**
>
> Azure Traffic Manager を使用すると、最も近い AKS クラスターとアプリケーション インスタンスにユーザーを誘導できます。 パフォーマンスと冗長性を最大限に高めるため、すべてのアプリケーション トラフィックは、Traffic Manager を介して AKS クラスターへ送るようにします。

異なるリージョンに AKS クラスターが複数ある場合は、Traffic Manager を使用して、各クラスターで実行されているアプリケーションへのトラフィック フローを制御します。 [Azure Traffic Manager](../traffic-manager/index.yml) は、ネットワーク トラフィックをリージョン間で分散することができる、DNS ベースのトラフィック ロード バランサーです。 Traffic Manager を使用して、クラスターの応答時間、または地理的な場所に基づいてユーザーをルーティングします。

![Traffic Manager を使用する AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

AKS クラスターが 1 つだけの場合は通常、特定のアプリケーションのサービス IP または DNS 名に接続します。 マルチ クラスターのデプロイでは、各 AKS クラスター上のサービスを指す Traffic Manager DNS 名に接続する必要があります。 これらのサービスは、Traffic Manager エンドポイントを使用して定義します。 各エンドポイントは、"*サービス ロード バランサー IP*" です。 この構成を使用して、あるリージョンの Traffic Manager エンドポイントから別のリージョンのエンドポイントにネットワーク トラフィックを送信します。

![Traffic Manager を使用した地理的ルーティング](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager によって DNS 検索が実行され、ユーザーに最適なエンドポイントが返されます。 入れ子になったプロファイルは、プライマリの場所の優先順位を付けることができます。 たとえば、最も近い地域的リージョンに接続する場合があります。 そのリージョンに問題がある場合、Traffic Manager によってセカンダリ リージョンにユーザーが誘導されます。 この方法により、最も近い地域的リージョンが使用できない場合でも、アプリケーション インスタンスに接続できるようになります。

エンドポイントとルーティングを設定する方法については、[Traffic Manager を使用した地理的トラフィック ルーティング方法の構成](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)に関する記事をご覧ください。

### <a name="application-routing-with-azure-front-door-service"></a>Azure Front Door Service を使用したアプリケーション ルーティング

[Azure Front Door Service](../frontdoor/front-door-overview.md) ではスプリット TCP ベースのエニーキャスト プロトコルが使用され、エンド ユーザーが最も近いフロント ドア POP (存在点) に迅速に接続されます。 Azure Front Door のその他の機能:
* TLS 終端
* カスタム ドメイン
* Web アプリケーション ファイアウォール
* URL Rewrite (URL 書き換え)
* セッション アフィニティ 

アプリケーション トラフィックのニーズを確認して、どのソリューションが最も適切かを検討してください。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>グローバル仮想ネットワーク ピアリングを使用してリージョンを相互接続する

[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を介して両方の仮想ネットワークを相互に接続し、クラスター間の通信を可能にします。 仮想ネットワーク ピアリングによって仮想ネットワークが相互に接続され、Microsoft の基幹ネットワーク全体、さらには地理的に異なるリージョンでも高帯域幅が提供されます。

実行中の AKS クラスターと仮想ネットワークをピアリングするには、AKS クラスターで標準のロード バランサーを使用します。 この前提条件により、Kubernetes サービスに仮想ネットワークのピアリングを介して到達可能になります。

## <a name="enable-geo-replication-for-container-images"></a>コンテナー イメージの geo レプリケーションを有効にする

> **ベスト プラクティス**
> 
> コンテナー イメージを Azure Container Registry に格納し、レジストリを各 AKS リージョンに geo レプリケーションします。

AKS でアプリケーションをデプロイして実行するには、コンテナー イメージを格納してプルするための手段が必要です。 Container Registry は AKS と統合することで、コンテナー イメージや Helm チャートを安全に格納することができます。 Container Registry は、マルチマスターの geo レプリケーションをサポートして、世界各地の Azure リージョンにイメージを自動的にレプリケートします。 

パフォーマンスと可用性の向上:
1. Container Registry の geo レプリケーションを使用すると、AKS クラスターが存在する各リージョンにレジストリを作成できます。 
1. これにより、各 AKS クラスターは、同じリージョン内のローカル コンテナー レジストリからコンテナー イメージをプルします。

![コンテナー イメージに対する Container Registry の geo レプリケーション](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Container Registry の geo レプリケーションを使用して同じリージョンからイメージをプルすると、次のような結果になります。

* **高速**: 同じ Azure リージョン内の高速で低遅延のネットワーク接続からイメージをプルします。
* **信頼性の向上**:リージョンが利用できない場合、AKS クラスターは、使用可能なコンテナー レジストリからイメージをプルします。
* **低コスト**: データセンター間のネットワーク エグレスの課金はありません。

geo レプリケーションは、*Premium* SKU コンテナー レジストリの機能です。 geo レプリケーションを構成する方法については、[Container Registry の geo レプリケーション](../container-registry/container-registry-geo-replication.md)に関する記事をご覧ください

## <a name="remove-service-state-from-inside-containers"></a>サービスの状態をコンテナー内から削除する

> **ベスト プラクティス**
> 
> コンテナー内にサービス状態を格納しないようにします。 代わりに、マルチリージョン レプリケーションをサポートする Azure のサービスとしてのプラットフォーム (PaaS) を使用します。

"*サービス状態*" とは、サービスが機能するために必要な、メモリ内またはディスク上のデータのことです。 これには、サービスによって読み書きされるデータ構造やメンバー変数が含まれます。 サービスの設計方法によっては、ディスクに格納されているファイルやその他のリソースが状態に含まれる場合があります。 たとえば、状態には、データとトランザクション ログを格納するためにデータベースで使用されるファイルが含まれる場合があります。

状態は、外部化することも、状態を操作するコードと同じ場所に配置することもできます。 通常、状態の外部化は、ネットワーク上のさまざまなコンピューターで実行されている、または同一コンピューター上でアウト オブ プロセスを実行しているデータベースやその他のデータ ストアを使用して行います。

コンテナーとマイクロサービスは、それらの内部で実行されているプロセスが状態を保持していないときに、回復性が最も高くなります。 ほとんどの場合、アプリケーションには何らかの状態が含まれているため、次のような PaaS ソリューションを使用します。
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL データベース

ポータブル アプリケーションをビルドするには、次のガイドラインを参照してください。

* [Twelve-Factor App Methodology](https://12factor.net/)
* [Web アプリケーションを複数の Azure リージョンで実行する](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>ストレージ移行プランの作成

> **ベスト プラクティス**
>
> Azure Storage を使用する場合は、ストレージをプライマリ リージョンからバックアップ リージョンに移行する方法を準備してテストします。

お使いのアプリケーションで、Azure Storage がデータに使用される場合があります。 その場合、アプリケーションは異なるリージョンにある複数の AKS クラスターに分散されます。 ストレージの同期を維持する必要があります。 ストレージをレプリケートする 2 つの一般的な方法は次のとおりです。

* インフラストラクチャベースの非同期レプリケーション
* アプリケーションベースの非同期レプリケーション

### <a name="infrastructure-based-asynchronous-replication"></a>インフラストラクチャベースの非同期レプリケーション

アプリケーションには、ポッドが削除された後も、永続的ストレージが必要な場合があります。 Kubernetes では、永続ボリュームを使用してデータ ストレージを保持することができます。 永続ボリュームはノード VM にマウントされてから、ポッドに公開されます。 永続ボリュームは、ポッドが同じクラスター内の別のノードに移動されても、ポッドに従います。

使用するレプリケーションの方法は、お使いのストレージ ソリューションによって決まります。 次の一般的なストレージ ソリューションによって、ディザスター リカバリーとレプリケーションに関する独自のガイダンスが提供されます。
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

通常、ユーザーがアプリケーションがデータを書き込む共通のストレージ ポイントを提供します。 次に、このデータはリージョン間でレプリケートされ、ローカルでアクセスされます。

![インフラストラクチャベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks を使用する場合、[Azure の Velero][velero] と [Kasten][kasten] を使用してレプリケーションとディザスター リカバリーの処理ができます。 これらのオプションは、Kubernetes ネイティブではあるが、サポートされていないバックアップ ソリューションです。

### <a name="application-based-asynchronous-replication"></a>アプリケーションベースの非同期レプリケーション

現在のところ、Kubernetes では、アプリケーションベースの非同期レプリケーションに対応したネイティブ実装は提供されていません。 コンテナーと Kubernetes は疎結合されているため、従来型のアプリケーションや言語アプローチはすべて機能するはずです。 通常は、アプリケーション自体がストレージ要求をレプリケートし、次にそれらが、各クラスターの基盤となるデータ ストレージに書き込まれます。

![アプリケーションベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターでのビジネス継続性とディザスター リカバリーに関する考慮事項に重点を置いて説明しました。 AKS でのクラスター操作の詳細については、ベスト プラクティスに関する次の記事を参照してください。

* [マルチテナント方式とクラスター分離][aks-best-practices-cluster-isolation]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/
