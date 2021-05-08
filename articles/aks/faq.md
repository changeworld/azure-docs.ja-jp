---
title: Azure Kubernetes Service (AKS) についてよく寄せられる質問
description: Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783199"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-currently-provide-aks"></a>現在はどの Azure リージョンで AKS が提供されていますか?

利用可能なリージョンの完全な一覧については、[AKS の利用可能なリージョン][aks-regions]に関するページを参照してください。

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>AKS クラスターを複数のリージョンに広げることはできますか?

いいえ。 AKS クラスターはリージョン リソースであり、複数のリージョンに広げることはできません。 複数のリージョンを含むアーキテクチャを作成する方法については、[ビジネス継続性とディザスター リカバリーのベストプラクティス][bcdr-bestpractices]に関する記事を参照してください。

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>AKS クラスターを複数の可用性ゾーンに広げることはできますか?

はい。 [可用性ゾーンをサポートしているリージョン][az-regions]内の 1 つ以上の[可用性ゾーン][availability-zones]に AKS クラスターをデプロイできます。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API サーバーにアクセスできるユーザーを制限できますか?

はい。 API サーバーへのアクセスを制限するためのオプションは 2 つあります。

- API サーバーのパブリック エンドポイントを維持するが、信頼できる IP 範囲へのアクセスを制限する場合は、[API サーバーの承認済み IP 範囲][api-server-authorized-ip-ranges]を使用します。
- API サーバーへのアクセスを仮想ネットワーク内から "*のみ*" に制限する場合は、[プライベート クラスター][private-clusters]を使用します。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>1 つのクラスター内で、異なる VM サイズを設定できますか?

はい。[複数のノード プール][multi-node-pools]を作成することにより、AKS クラスターで異なる仮想マシン サイズを使用できます。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

Azure では、セキュリティ更新プログラムが夜間スケジュールでご利用のクラスター内の Linux ノードに自動的に適用されます。 ただし、必要な場合は、それらの Linux ノードが確実に再起動されるようにする必要があります。 ノードを再起動するにはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターでは、[cordon ノードと drain ノード][cordon-drain]が自動的にアップグレードされてから、最新の Ubuntu イメージと、新しいパッチ バージョンまたは Kubernetes のマイナー バージョンで、新しいノードがオンラインにされます。 詳細については、「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください。
- [ノード イメージのアップグレード](node-image-upgrade.md)を使用します。

### <a name="windows-server-nodes"></a>Windows Server ノード

Windows Server ノードでは、Windows Update が自動的に実行され、最新の更新プログラムが適用されることはありません。 Windows Update のリリース サイクルと独自の検証プロセス前後の定期的スケジュールで、AKS クラスター内のクラスターと Windows Server ノード プールでアップグレードを実行する必要があります。 このアップグレード プロセスでは、最新の Windows Server イメージと修正プログラムを実行するノードが作成されて、古いノードが削除されます。 このプロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

AKS は、仮想マシン スケール セット、仮想ネットワーク、マネージド ディスクなど、さまざまな Azure インフラストラクチャ リソースに基づいて構築されています。 これにより、AKS によって提供されるマネージド Kubernetes 環境内で、Azure プラットフォームのコア機能の多くを活用できます。 たとえば、ほとんどの種類の Azure 仮想マシンは AKS で直接使用できます。また Azure Reservations を使用して、それらのリソースに対する割引を自動的に受け取ることができます。

このアーキテクチャを有効にするため、各 AKS デプロイは、2 つのリソース グループにまたがっています。

1. 最初のリソース グループを作成します。 このグループには、Kubernetes サービスのリソースのみが含まれます。 AKS リソース プロバイダーにより、デプロイの間に 2 番目のリソース グループが自動的に作成されます。 2 番目のリソース グループの例は、*MC_myResourceGroup_myAKSCluster_eastus* です。 この 2 つ目のリソース グループの名前を指定する方法については、次のセクションをご覧ください。
1. *ノード リソース グループ* と呼ばれる 2 つ目のリソース グループには、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これらのリソースには、Kubernetes ノードの VM、仮想ネットワー キング、およびストレージが含まれます。 既定では、ノード リソース グループには *MC_myResourceGroup_myAKSCluster_eastus* のような名前が付いています。 AKS は、クラスターが削除されるたびにノード リソースを自動的に削除するため、クラスターのライフサイクルを共有するリソースにのみ使用する必要があります。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS ノード リソース グループに独自の名前を指定できますか?

はい。 既定では、AKS によってノード リソース グループに *MC_resourcegroupname_clustername_location* という名前が設定されますが、独自の名前を指定することもできます。

独自のリソース グループ名を指定するには、[aks-preview][aks-preview-cli] Azure CLI 拡張機能バージョン *0.3.2* 以降をインストールします。 [az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときに、 `--node-resource-group` パラメーターを使用してリソース グループの名前を指定します。 [Azure Resource Manager テンプレートを使用][aks-rm-template]して AKS クラスターをデプロイする場合は、*nodeResourceGroup* プロパティを使用してリソース グループ名を定義できます。

* セカンダリ リソース グループは、自分のサブスクリプションの Azure リソース プロバイダーによって自動的に作成されます。
* カスタム リソース グループの名前を指定できるのは、クラスターを作成するときだけです。

ノード リソース グループを使うときは、次のことができないので注意してください。

* ノード リソース グループに対して既存のリソース グループを指定すること。
* ノード リソース グループに対して異なるサブスクリプションを指定すること。
* クラスターが作成された後で、ノード リソース グループ名を変更すること。
* ノード リソース グループ内の管理対象リソースに名前を指定すること。
* ノード リソース グループ内の管理対象リソースの Azure で作成されたタグを変更または削除すること。 (詳しくは、次のセクションで追加の情報を参照してください。)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>ノード リソース グループ内の AKS リソースのタグや他のプロパティを変更できますか?

ノード リソース グループ内の Azure で作成されたタグや他のリソース プロパティを変更または削除する場合、スケーリングやアップグレードのエラーなど、予期しない結果になる可能性があります。 AKS を使用すると、カスタム タグを作成したり、エンド ユーザーが作成したものを変更したりできます。また、[ノード プールを作成](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)するときにそれらのタグを追加できます。 たとえばビジネス単位やコスト センターを割り当てるために、カスタム タグを作成または変更することがあります。 これは、マネージド リソース グループにスコープを指定して Azure ポリシーを作成することによっても実現できます。

ただし、AKS クラスター内のノード リソース グループにあるリソース上で **Azure によって作成されたタグ** を変更することは、サポートされていないアクションであり、サービス レベル目標 (SLO) が損なわれます。 詳細については、「[AKS でサービス レベル アグリーメントは提供されますか?](#does-aks-offer-a-service-level-agreement)」を参照してください。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS ではどのような Kubernetes アドミッション コントローラーがサポートされますか? アドミッション コントローラーの追加や削除はできますか?

AKS では、以下の[アドミッション コントローラー][admission-controllers]がサポートされています。

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

現在は、AKS でアドミッション コントローラーの一覧を変更することはできません。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>AKS でアドミッション コントローラー Webhook を使用できますか?

はい、AKS でアドミッション コントローラー Webhook を使用できます。 **control-plane ラベル** でマークされた内部 AKS 名前空間を除外することをお勧めします。 たとえば、以下を Webhook 構成に追加します。

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS によって、API サーバーのエグレスがファイアウォールで保護されるため、アドミッション コントローラー Webhook にクラスター内からアクセスできる必要があります。

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>アドミッション コントローラー Webhook は kube-system と内部 AKS 名前空間に影響しますか?

システムの安定性を保護し、カスタムのアドミッション コントローラーが kube-system の内部サービスに影響を与えないようにするために、名前空間 AKS には **Admissions Enforcer** があります。これにより、kube-system と AKS の内部名前空間が自動的に除外されます。 このサービスにより、カスタムのアドミッション コントローラーは、kube-system で実行されているサービスに影響しなくなります。

カスタムのアドミッション webhook の対象にする必要のある kube-system (推奨されません) に、何かをデプロイするための重要なユースケースがある場合は、次のラベルまたは注釈を追加して、Admissions Enforcer によってそれが無視されるようにすることができます。

ラベル: ```"admissions.enforcer/disabled": "true"``` または注釈: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

AKS は現在、Azure Key Vault とネイティブに統合されていません。 ただし、[CSI シークレット ストア用の Azure Key Vault プロバイダー][csi-driver] を使用して、Kubernetes ポッドから Key Vault のシークレットに直接統合することはできます。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

はい、Windows Server コンテナーは AKS で利用できます。 AKS で Windows Server コンテナーを実行するには、Windows Server をゲスト OS として実行するノード プールを作成します。 Windows Server コンテナーでは、Windows Server 2019 のみを使用できます。 開始するには、[Windows Server ノード プールでの AKS クラスターの作成][aks-windows-cli]に関するページを参照してください。

Windows Server によるノード プールのサポートには、Kubernetes プロジェクトの上流の Windows Server の一部であるいくつかの制限が含まれます。 これらの制限の詳細については、[AKS での Windows Server コンテナーの制限事項][aks-windows-limitations]に関するページを参照してください。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS でサービス レベル アグリーメントは提供されますか?

AKS では、[アップタイム SLA][uptime-sla] を使用したオプションのアドオン機能として SLA 保証が提供されます。 

既定で提供される Free SKU にはサービス レベル "*アグリーメント*" は関連付けられていませんが、サービス レベル "*目標*" は 99.5% です。 アップグレード、異常なアンダーレイ ノード、プラットフォームのメンテナンス、アプリケーションによる API サーバーへの過剰な要求などで、一時的な接続の問題が見られる可能性があります。ワークロードで API サーバーの再起動を許容できない場合は、アップタイム SLA を使用することをお勧めします。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>自分の AKS エージェント ノードに Azure の予約割引を適用できますか?

AKS エージェント ノードは、標準の Azure 仮想マシンとして課金されます。したがって、AKS で使用している VM サイズに対して [Azure の予約][reservation-discounts]を購入している場合、それらの割引が自動的に適用されます。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Azure テナント間でクラスターを移動/移行することはできますか?

テナント間での AKS クラスターの移動は現在サポートされていません。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>サブスクリプション間でクラスターを移動/移行することはできますか?

サブスクリプション間でのクラスターの移動は現在サポートされていません。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS クラスターを現在の Azure サブスクリプションから別のサブスクリプションへ移動することはできますか?

Azure サブスクリプション間での AKS クラスターおよびその関連リソースの移動はサポートされていません。

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>AKS クラスターまたは AKS インフラストラクチャ リソースをその他のリソース グループに移動したり、名前を変更したりできますか。

AKS クラスターやその関連リソースの移動と名前変更はサポートされていません。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>クラスターの削除に時間がかかるのはなぜですか?

ほとんどのクラスターはユーザーの要求に応じて削除されます。場合によっては、特に顧客が独自のリソース グループを持ち込んでいたり、リソース グループ間のタスクの削除を行っている場合に、さらに時間がかかったり、失敗することがあります。 削除に関する問題が発生した場合は、リソース グループをロックしていないこと、リソース グループ外のリソースのリソース グループとの関連付けが解除されていることなどを再確認してください。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>'NodeLost' または 'Unknown' の状態のポッドまたはデプロイがある場合でも、クラスターをアップグレードできますか?

可能ですが、AKS ではこれをお勧めしていません。 アップグレードは、クラスターの状態がわかっており正常な場合に実行する必要があります。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>異常な状態のノードやシャットダウンしているノードを 1 つ以上含むクラスターがある場合に、アップグレードを実行できますか?

いいえ。エラー状態や、それ以外にクラスターから取り除かれているノードはすべて、アップグレード前に削除または除去してください。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>クラスターの削除を実行しましたが、`[Errno 11001] getaddrinfo failed` のエラーが表示されます

最も一般的な原因は、まだ使用中でクラスターに関連付けられている 1 つ以上のネットワーク セキュリティ グループ (NSG) をユーザーが保持していることです。  これらを取り除いてから、もう一度削除を試してください。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>アップグレードを実行しましたが、ポッドがクラッシュ ループの状態になりました。準備プローブが失敗していますか?

サービス プリンシパルの有効期限が切れていないことを確認してください。  参照:[AKS サービス プリンシパル](./kubernetes-service-principal.md)と [AKS の資格情報の更新](./update-credentials.md)に関するページを参照してください。

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>クラスターは動作していましたが、突然、LoadBalancers のプロビジョニングや PVC のマウントなどができなくなりました。

サービス プリンシパルの有効期限が切れていないことを確認してください。  参照:[AKS サービス プリンシパル](./kubernetes-service-principal.md)と [AKS の資格情報の更新](./update-credentials.md)に関するページを参照してください。

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>AKS クラスターを 0 (ゼロ) にスケーリングできますか?
[実行中の AKS クラスター](start-stop-cluster.md)を完全に停止して、それぞれのコンピューティング コストを節約できます。 さらに、必要なクラスター構成のみを維持しながら、[すべてまたは特定の `User` ノード プールを 0 にスケーリングまたは自動スケーリング](scale-cluster.md#scale-user-node-pools-to-0)することもできます。
[システム ノード プール](use-system-pools.md)をゼロに直接スケーリングすることはできません。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>仮想マシン スケール セット API を使用して手動でスケーリングできますか?

いいえ、仮想マシン スケール セット API を使用したスケール操作はサポートされていません。 AKS API (`az aks scale`) を使用してください。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>仮想マシン スケール セットを使用して、ゼロ ノードに手動でスケーリングできますか?

いいえ、仮想マシン スケール セット API を使用したスケール操作はサポートされていません。 AKS API を使用してゼロの非システム ノード プールにスケーリングするか、代わりに[クラスターを停止する](start-stop-cluster.md)ことができます。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>すべての VM を停止したり、その割り当てを解除したりできますか?

AKS には、このような構成に耐え、そこから復旧するための回復性メカニズムがありますが、これはサポートされている構成ではありません。 代わりに、[クラスターを停止してください](start-stop-cluster.md)。

## <a name="can-i-use-custom-vm-extensions"></a>カスタム VM 拡張機能を使用できますか?

Log Analytics エージェントは、Microsoft によって管理される拡張機能であるため、サポートされています。 それ以外は、いいえです。AKS はマネージド サービスであり、IaaS リソースの操作はサポートされていません。 カスタム コンポーネントをインストールするには、Kubernetes API とメカニズムを使用します。 たとえば、必要なコンポーネントをインストールするには、デーモンセットを使用します。

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS によって、クラスターのリージョン外に格納される顧客データはありますか?

現在、顧客データを 1 つのリージョンに格納できるようにする機能は、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。

## <a name="are-aks-images-required-to-run-as-root"></a>AKS イメージはルートとして実行する必要がありますか?

次の 2 つのイメージを除き、AKS イメージはルートとして実行する必要はありません。

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Azure CNI の透過モードとブリッジ モードとは

v1.2.0 以降、Azure CNI では、シングル テナント Linux CNI デプロイには既定として透過モードが設定されます。 透過モードは、ブリッジ モードに代わるものです。 このセクションでは、両方のモードの違いと、Azure CNI で透過モードを使用する場合の利点と制限事項について説明します。

### <a name="bridge-mode"></a>ブリッジ モード

その名前が示すように、ブリッジ モードの Azure CNI は "Just In Time" 方式で、"azure0" という名前の L2 ブリッジを作成します。 ホスト側ポッド `veth` ペアインターフェイスはすべて、このブリッジに接続されます。 そのため、ポッド間 VM 内通信とその残りのトラフィックは、このブリッジを通過します。 対象となるブリッジはレイヤー 2 仮想デバイスです。これは、1 つ以上の実際のデバイスをそれにバインドしない限り、それ自体では何も受信したり、送信したりできません。 このため、Linux VM の eth0 を "azure0" ブリッジの配下に変換する必要があります。 これにより、Linux VM 内に複雑なネットワークトポロジが作成され、その結果として、CNI で DNS サーバーの更新などの他のネットワーク機能を処理する必要がありました。

:::image type="content" source="media/faq/bridge-mode.png" alt-text="ブリッジ モードのトポロジ":::

下に、ブリッジ モードでの IP ルート セットアップの例を示します。 ノードに含まれるポッドの数に関係なく、ルートは常に 2 つだけになります。 最初のものは、azure0 上のローカルを除くすべてのトラフィックが、IP "src 10.240.0.4" (これはノードのプライマリ IP です) を持つインターフェイスを介してサブネットの既定のゲートウェイに送られることを示し、2 番目のものは、カーネルが決定する、カーネルに対する "10.20.x.x" ポッド スペースを示しています。

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>透過モード
透過モードでは、Linux ネットワークを設定するために簡単な方法が採られます。 このモードでは、Linux VM の eth0 インターフェイスのプロパティは Azure CNI によって変更されません。 Linux ネットワークのプロパティを変更するこの最小限の方法は、クラスターがブリッジ モードで直面する可能性のある複雑でやっかいなケースの問題を軽減するのに役立ちます。 透過モードでは、Azure CNI によって、ホスト ネットワークに追加されるホスト側ポッド `veth` ペア インターフェイスが作成および追加されます。 VM 内ポッド間通信は、CNI によって追加される IP ルート経由で行われます。 基本的には、ポッド間通信はレイヤー 3 を経由し、ポッド トラフィックは L3 ルーティング規則によってルーティングされます。

:::image type="content" source="media/faq/transparent-mode.png" alt-text="透過モードのトポロジ":::

下に、透過モードの IP ルート セットアップの例を示します。ポッドとして接続先 IP を持つトラフィックがポッドのホスト側の `veth` ペア インターフェイスに直接送信されるように、各ポッドのインターフェイスでは静的ルートが接続されます。

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>透過モードの利点

- ノード ローカル DNS を設定することなく、`conntrack` DNS 並列競合状態の軽減策と、5 秒間の DNS 待機時間の問題の回避策を提供します (パフォーマンス上の理由から、ノード ローカル DNS を使用することもできます)。
- "Just In Time" ブリッジのセットアップによって、CNI ブリッジ モードで現在発生する 5 秒間の初期 DNS 待機時間が無くなります。
- ブリッジ モードでのやっかいなケースの 1 つは、ユーザーが VNET または NIC に追加するカスタム DNS サーバーの一覧を Azure CNI で更新し続けることができないことです。 この結果、CNI は DNS サーバーの一覧の最初のインスタンスのみを取得します。 透過モードでは解決されました。どの eth0 プロパティも CNI で変更されないためです。 詳細については、[こちら](https://github.com/Azure/azure-container-networking/issues/713)を参照してください。
- ARP がタイムアウトになったときに、UDP トラフィックをより適切に処理し、UDP フラッド ストームの軽減策を提供します。ブリッジ モードでは、ブリッジが VM 内ポッド間通信で送信先ポッドの MAC アドレスを認識しない場合、設計上、すべてのポートに大量のパケットが送信されます。 透過モードでは解決されました。パスに L2 デバイスがないためです。 詳細については、[こちら](https://github.com/Azure/azure-container-networking/issues/704)を参照してください。
- 透過モードは、ブリッジ モードと比較した場合、スループットと待機時間の観点から VM 内ポッド間通信においてより優れたパフォーマンスを提供します。

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>ボリュームに大量のファイルがある場合に、権限の所有権の設定による低速化の問題を回避するには、どのようにしますか?

従来、ポッドが root 以外のユーザーとして実行される場合 (この場合)、ポッドによるボリュームの読み取りと書き込みを可能にするために、ポッドのセキュリティ コンテキスト内で `fsGroup` を指定する必要があります。 この要件の詳細については、[こちら](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)を参照してください。

しかし、`fsGroup` を設定することの副作用の 1 つとして、ボリュームがマウントされるたびに、Kubernetes では、下に示すいくつかの例外を除き、ボリューム内のすべてのファイルとディレクトリに対して `chown()` と `chmod()` を再帰的に実行する必要があるということがあります。 これは、ボリュームのグループ所有権が要求した `fsGroup` と既に一致している場合でも発生し、小さいファイルが多数ある大容量のボリュームでは、ポッドの起動時間が長くなり、コストが非常に高くなる可能性があります。 このシナリオは v1.20 より前の既知の問題であり、回避策はポッドが root として実行されるように設定することです。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

この問題は Kubernetes v1.20 で解決されました。詳細については、[Kubernetes 1.20:ボリュームの権限変更の詳細制御](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/)に関するページを参照してください。


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
