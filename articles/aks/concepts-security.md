---
title: 概念 - Azure Kubernetes Service (AKS) におけるセキュリティ
description: Azure Kubernetes Service (AKS) におけるセキュリティ (マスターとノードの通信、ネットワーク ポリシー、Kubernetes シークレットなど) について説明します。
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105308"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションとクラスターに対するセキュリティの概念

クラスターのセキュリティは、Azure Kubernetes Service (AKS) でアプリケーションのワークロードを実行する際に顧客データを保護します。 

Kubernetes には、*ネットワーク ポリシー* や *シークレット* などのセキュリティ コンポーネントが含まれています。 一方、Azure には、ネットワーク セキュリティ グループや調整されたクラスターのアップグレードなどのコンポーネントが含まれています。 AKS は、これらのセキュリティ コンポーネントを組み合わせて以下を実現します。
* AKS クラスターで常に最新の OS セキュリティ更新プログラムと Kubernetes リリースが実行されるようにします。
* セキュリティで保護されたポッド トラフィックと、機密性の高い資格情報へのアクセスを提供します。

この記事では、AKS 内でアプリケーションをセキュリティで保護する主要な概念について説明します。

- [Azure Kubernetes Service (AKS) でのアプリケーションとクラスターに対するセキュリティの概念](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [マスターのセキュリティ](#master-security)
  - [ノードのセキュリティ](#node-security)
    - [コンピューティングの分離](#compute-isolation)
  - [クラスターのアップグレード](#cluster-upgrades)
    - [切断およびドレイン](#cordon-and-drain)
  - [ネットワークのセキュリティ](#network-security)
    - [Azure ネットワーク セキュリティ グループ](#azure-network-security-groups)
  - [Kubernetes シークレット](#kubernetes-secrets)
  - [次の手順](#next-steps)

## <a name="master-security"></a>マスターのセキュリティ

AKS の場合、Kubernetes マスター コンポーネントは、Microsoft が提供、管理、および保守するマネージド サービスの一部です。 各 AKS クラスターには、API サーバーやスケジューラなどを提供する独自シングル テナントの専用 Kubernetes マスターがあります。

既定では、Kubernetes API サーバーは、パブリック IP アドレスと完全修飾ドメイン名 (FQDN) を使用します。 [許可された IP 範囲][authorized-ip-ranges]を使用して、API サーバー エンドポイントへのアクセスを制限できます。 また、フル [プライベート クラスター][private-clusters]を作成して、API サーバーから仮想ネットワークへのアクセスを制限することもできます。

API サーバーへのアクセスは、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) と Azure RBAC を使用して制御できます。 詳細については、[Azure AD と AKS の統合][aks-aad]に関するページを参照してください。

## <a name="node-security"></a>ノードのセキュリティ

AKS ノードは、ユーザーが管理および保守する Azure 仮想マシン (VM) です。 
* Linux ノードは、`containerd` または Moby コンテナー ランタイムを使用して、最適化された Ubuntu ディストリビューションを実行します。 
* Windows Server ノードは、`containerd` または Moby コンテナー ランタイムを使用して、最適化された Windows Server 2019 リリースを実行します。 

AKS クラスターを作成またはスケールアップすると、ノードは自動的に、最新の OS セキュリティ更新プログラムと構成でデプロイされます。

> [!NOTE]
> AKS クラスターで使用される内容について:
> * Kubernetes バージョン 1.19 以降のノード プールの場合、コンテナー ランタイムとして `containerd` を使用します。 
> * Kubernetes バージョン 1.19 以前のノード プールの場合、コンテナー ランタイムとして [Moby](https://mobyproject.org/) (アップストリーム Docker) を使用します。

### <a name="node-security-patches"></a>ノードのセキュリティ パッチ

#### <a name="linux-nodes"></a>Linux ノード
Azure プラットフォームでは、OS セキュリティ修正プログラムが夜間スケジュールで Linux ノードに自動的に適用されます。 Linux OS セキュリティ更新プログラムによってホストの再起動が求められた場合、自動的には再起動しません。 次のいずれかを実行できます。
* Linux ノードを手動で再起動します。
* Kubernetes 用オープン ソースの再起動デーモンである [Kured][kured] を使用します。 Kured は [DaemonSet][aks-daemonsets] として実行され、再起動が必要であることを示すファイルについて各ノードを監視します。 

再起動は、クラスター アップグレードと同じ[切断およびドレイン プロセス](#cordon-and-drain)を使用するクラスターで管理されます。

#### <a name="windows-server-nodes"></a>Windows Server ノード

Windows Server ノードの場合、Windows Update によって最新の更新プログラムが実行されて適用されるわけではありません。 通常の Windows Update リリース サイクルと独自の検証プロセスの前後に、AKS クラスターで Windows Server ノード プールのアップグレードをスケジュールします。 このアップグレード プロセスでは、最新の Windows Server イメージと修正プログラムを実行するノードが作成されて、古いノードが削除されます。 このプロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

### <a name="node-deployment"></a>ノードのデプロイ
ノードは、パブリック IP アドレスが割り当てられていないプライベート仮想ネットワーク サブネットにデプロイされます。 トラブルシューティングと管理の目的で、SSH は既定で有効になっており、内部 IP アドレスを使用してのみアクセスできます。

### <a name="node-storage"></a>ノード ストレージ
ストレージを提供するために、ノードは Azure Managed Disks を使用します。 ほとんどの VM ノード サイズでは、Azure Managed Disks は高性能 SSD によってサポートされる Premium ディスクです。 マネージド ディスクに格納されたデータは、Azure プラットフォームへの保存時に自動的に暗号化されます。 冗長性を向上させるために、Azure Managed Disks は Azure データ センター内で安全にレプリケートされます。

### <a name="hostile-multi-tenant-workloads"></a>悪意のあるマルチテナント ワークロード

現在、Kubernetes 環境は悪意のあるマルチテナントの使用に対して安全ではありません。 *ポッドのセキュリティ ポリシー* やノード用の Kubernetes RBAC などのような追加のセキュリティ機能により、悪用を効率的にブロックします。 悪意のあるマルチテナント ワークロードを実行する場合の真のセキュリティを実現するために、ハイパーバイザーのみを信頼してください。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 

この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。 ワークロードを分離する方法については、「[AKS でのクラスターの分離に関するベスト プラクティス][cluster-isolation]」を参照してください。

### <a name="compute-isolation"></a>コンピューティングの分離

コンプライアンスや規制上の要件により、特定のワークロードでは、他の顧客によるワークロードから高度に分離する必要がある場合があります。 これらのワークロードに対して、Azure は、AKS クラスター内のエージェント ノードとして使用する[分離された仮想マシン](../virtual-machines/isolation.md)を提供します。 これらの分離された仮想マシンは特定のハードウェアの種類に分離され、単一顧客専用になります。 

AKS クラスターの作成時、またはノード プールの追加時には、**ノード サイズ** として[分離された仮想マシン サイズの 1 つ](../virtual-machines/isolation.md)を選択します。

## <a name="cluster-upgrades"></a>クラスターのアップグレード

Azure には、AKS クラスターとコンポーネントのアップグレード、セキュリティとコンプライアンスの維持、最新機能へのアクセスを行うためのアップグレード オーケストレーション ツールが用意されています。 このアップグレードの調整には、Kubernetes のマスターとエージェントの両方のコンポーネントが含まれます。 

アップグレード プロセスを開始するには、[リストに記載されている使用可能な Kubernetes バージョン](supported-kubernetes-versions.md)のいずれかを指定します。 次に、Azure によって各 AKS ノードが安全に切断およびドレインされ、アップグレードが実行されます。

### <a name="cordon-and-drain"></a>切断およびドレイン

アップグレード プロセス中、AKS ノードはクラスターから個別に切断され、新しいポッドがノードにスケジュールされないようにします。 ノードはその後でドレインされ、次のようにアップグレードされます。

1.  新しいノードは、ノード プールにデプロイされます。 
    * このノードは、最新の OS イメージと修正プログラムを実行します。
1. 既存のノードのいずれかで、アップグレードが識別されます。 
1. 識別されたノード上のポッドは正常に終了し、ノード プール内にある他のノードでスケジュールされます。
1. 空のノードは、AKS クラスターから削除されます。
1. アップグレード プロセスの一環としてすべてのノードが正常に置き換えられるまで、手順 1 ～ 4 が繰り返されます。

詳細については、「[AKS クラスターのアップグレード][aks-upgrade-cluster]」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

オンプレミス ネットワークの接続とセキュリティのために、既存の Azure 仮想ネットワーク サブネットに AKS クラスターをデプロイすることができます。 これらの仮想ネットワークは、Azure サイト間 VPN または Express Route を使用してオンプレミス ネットワークに接続します。 プライベートの内部 IP アドレスを使用して Kubernetes イングレス コントローラーを定義し、内部ネットワーク接続へのサービス アクセスを制限します。

### <a name="azure-network-security-groups"></a>Azure ネットワーク セキュリティ グループ

仮想ネットワークのトラフィック フローをフィルター処理する場合、Azure ではネットワーク セキュリティ グループのルールが使用されます。 これらのルールは、リソースへのアクセスを許可または拒否する発信元と宛先の IP 範囲、ポート、およびプロトコルを定義します。 Kubernetes API サーバーへの TLS トラフィックを許可する、既定の規則が作成されます。 ロード バランサー、ポート マッピング、またはイングレス ルートを使用してサービスを作成します。 AKS は、トラフィック フローのネットワーク セキュリティ グループを自動的に変更します。

AKS クラスターに独自のサブネットを指定する場合は、AKS が管理するサブネットレベルのネットワーク セキュリティ グループを変更 **しないでください**。 代わりに、サブネットレベルのネットワーク セキュリティ グループをさらに作成して、トラフィックのフローを変更します。 ロード バランサーへのアクセス、コントロール プレーンとの通信、および[エグレス][aks-limit-egress-traffic]など、クラスターを管理するために必要なトラフィックに干渉しないようにしてください。

### <a name="kubernetes-network-policy"></a>Kubernetes ネットワーク ポリシー

クラスター内のポッド間のネットワーク トラフィックを制限するために、AKS では、[Kubernetes ネットワーク ポリシー][network-policy]のサポートを提供しています。 ネットワーク ポリシーを使用すると、名前空間とラベル セレクターに基づいて、クラスター内の特定のネットワーク パスを許可または拒否できます。

## <a name="kubernetes-secrets"></a>Kubernetes シークレット

Kubernetes *シークレット* を使用すると、アクセス資格情報やキーなどの機密データをポッドに挿入できます。 
1. Kubernetes API を使用してシークレットを作成します。 
1. ポッドまたはデプロイを定義し、特定のシークレットを要求します。 
    * シークレットは、それを必要とするスケジュールされたポッドを持つノードにのみ提供されます。
    * シークレットは *tmpfs* に格納され、ディスクには書き込まれません。 
1. シークレットを必要とするノードの最後のポッドを削除すると、ノードの tmpfs からシークレットが削除されます。 
   * シークレットは、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。

シークレットを使用すると、ポッドやサービスの YAML マニフェストに定義されている機密情報が削減されます。 代わりに、YAML マニフェストの一部として Kubernetes API サーバーに格納されたシークレットを要求します。 この方法により、シークレットへのアクセス権が特定のポッドにのみ提供されます。 

> [!NOTE]
> 生のシークレット マニフェスト ファイルには、base64 形式の機密データが含まれています (詳細については、[公式ドキュメント][secret-risks]を参照してください)。 これらのファイルは機密情報として扱い、ソース管理にはコミットしないようにしてください。

Kubernetes シークレットは、分散型キー値ストアである etcd に格納されます。 etcd ストアは AKS によって完全に管理されており、[データは Azure プラットフォーム内での保存時に暗号化されます][encryption-atrest]。 

## <a name="next-steps"></a>次のステップ

AKS クラスターのセキュリティでの保護を開始するには「[AKS クラスターのアップグレード][aks-upgrade-cluster]」を参照してください。

関連付けられているベスト プラクティスについては、[AKS でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][operator-best-practices-cluster-security]のページと、[AKS でのポッドのセキュリティに関するベスト プラクティス][developer-best-practices-pod-security]のページを参照してください。

コア Kubernetes と AKS の概念に関する詳細については、以下を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS の ID][aks-concepts-identity]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
