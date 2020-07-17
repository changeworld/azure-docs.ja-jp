---
title: Azure Kubernetes Service (AKS) での可用性ゾーンの使用
description: Azure Kubernetes Service (AKS) で複数の可用性ゾーンにノードを分散させるクラスターを作成する方法について説明します。
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596812"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する

Azure Kubernetes Service (AKS) クラスターは、ノードやストレージなどのリソースを、基になる Azure コンピューティング インフラストラクチャの複数の論理セクションに分散させます。 このデプロイ モデルを使用すると、単一の Azure データセンター内の個別の更新ドメインと障害ドメインでノードを確実に実行できます。 この既定の動作でデプロイされる AKS クラスターは、ハードウェア障害または計画メンテナンス イベントから保護するための高レベルの可用性を提供します。

アプリケーションに高レベルの可用性を提供するために、AKS クラスターを複数の可用性ゾーンに分散させることができます。 これらのゾーンは、特定のリージョン内の物理的に分離されたデータセンターです。 クラスター コンポーネントが複数のゾーンに分散されている場合、AKS クラスターは、それらのゾーンのいずれかで障害が発生しても許容できます。 アプリケーションと管理操作は、1 つのデータセンター全体に問題がある場合でも継続して利用できます。

この記事では、AKS クラスターを作成し、複数の可用性ゾーンにノード コンポーネントを分散させる方法について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="limitations-and-region-availability"></a>制限事項とリージョンの可用性

現在、AKS クラスターは、次のリージョンの可用性ゾーンを使用して作成できます。

* 米国中部
* 米国東部 2
* 米国東部
* フランス中部
* 東日本
* 北ヨーロッパ
* 東南アジア
* 英国南部
* 西ヨーロッパ
* 米国西部 2

可用性ゾーンを使用して AKS クラスターを作成する場合、次の制限事項が適用されます。

* 可用性ゾーンは、クラスターの作成時にのみ有効にできます。
* 可用性ゾーンの設定は、クラスターの作成後は更新できません。 また、可用性ゾーンを使用するために、既存の非可用性ゾーン クラスターを更新することはできません。
* AKS クラスターの作成後、その可用性ゾーンを無効にすることはできません。
* 選択したノード サイズ (VM SKU) を、すべての可用性ゾーンで使用できる必要があります。
* 可用性ゾーンが有効になっているクラスターでは、複数のゾーンへの分散のために Azure Standard Load Balancer を使用する必要があります。
* Standard Load Balancer をデプロイするために、Kubernetes バージョン 1.13.5 以降を使用する必要があります。

可用性ゾーンを使用する AKS クラスターでは、Azure Load Balancer *Standard* SKU を使用する必要があります。これはロード バランサーの種類の既定値です。 このロード バランサーの種類は、クラスターの作成時にのみ定義できます。 Standard Load Balancer の詳細と制限事項については、[Azure Load Balancer Standard SKU の制限事項][standard-lb-limitations]に関する記事をご覧ください。

### <a name="azure-disks-limitations"></a>Azure ディスクの制限事項

Azure マネージド ディスクを使用するボリュームは、現在、ゾーン リソースではありません。 元のゾーンとは異なるゾーンで再スケジュールされたポッドは、以前のディスクに再アタッチできません。 ゾーンの問題が発生する可能性のある永続的なストレージを必要としないステートレス ワークロードを実行することをお勧めします。

ステートフル ワークロードを実行する必要がある場合は、ポッド仕様でテイントと容認を使用して、ディスクと同じゾーンにポッドを作成するよう Kubernetes スケジューラに指示します。 または、ゾーン間でスケジュールされているときに、ポッドにアタッチできる Azure Files などのネットワークベースのストレージを使用します。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS クラスターの可用性ゾーンの概要

可用性ゾーンとは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 ゾーンは、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 可用性ゾーンはリージョン内で物理的に分離されているため、データセンターで障害が発生してもアプリケーションとデータは保護されます。 ゾーン冗長サービスによってアプリケーションとデータが可用性ゾーン全体にレプリケートされ、単一障害点から保護されます。

詳しくは、「[Azure の可用性ゾーンの概要][az-overview]」をご覧ください。

可用性ゾーンを使用してデプロイされる AKS クラスターでは、1 つのリージョン内の複数のゾーンにノードを分散させることができます。 たとえば、 *米国東部 2*  リージョンのクラスターでは、*米国東部 2* の 3 つすべての可用性ゾーンでノードを作成できます。 この AKS クラスターリソースの分散により、特定のゾーンの障害に対する回復力があるため、クラスターの可用性が向上します。

![複数の可用性ゾーンへの AKS ノードの分散](media/availability-zones/aks-availability-zones.png)

ゾーンの停止時には、手動で、またはクラスター オートスケーラーを使用してノードを再調整できます。 1 つのゾーンが使用不可になっても、アプリケーションは引き続き実行されます。

## <a name="create-an-aks-cluster-across-availability-zones"></a>複数の可用性ゾーンでの AKS クラスターの作成

[az aks create][az-aks-create] コマンドを使用してクラスターを作成する場合、`--zones` パラメーターで、エージェント ノードをデプロイする先のゾーンを定義します。 また、クラスターの作成時に `--zones` パラメーターを定義すると、クラスターの AKS コントロール プレーンのコンポーネントは、最高の可用性構成の複数のゾーンに分散されます。

AKS クラスターの作成時に既定のエージェント プールのゾーンを定義しない場合、クラスターの AKS コントロール プレーン コンポーネントは可用性ゾーンを使用しません。 [az aks nodepool add][az-aks-nodepool-add] コマンドを使用して追加のノード プールを追加し、それらの新しいノードに対して `--zones` を指定できますが、コントロール プレーン コンポーネントは可用性ゾーンを認識しません。 ノード プールまたは AKS コントロール プレーン コンポーネントがデプロイされた後、それらのゾーン認識は変更できません。

次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前の AKS クラスターを作成しています。 合計 *3* つのノードが作成されます (ゾーン *1* にエージェント 1 つ、ゾーン *2* にエージェント 1 つ、ゾーン *3* にエージェント 1 つ)。 また、AKS コントロール プレーンのコンポーネントは、クラスターの作成プロセスの一部として定義されるため、最高の可用性構成の複数のゾーンに分散されます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

AKS クラスターの作成には数分かかります。

## <a name="verify-node-distribution-across-zones"></a>複数のゾーンへのノードの分散を確認する

クラスターの準備ができたら、スケール セット内のエージェント ノードを一覧表示して、それらがデプロイされている可用性ゾーンを確認します。

最初に、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

次に、[kubectl describe][kubectl-describe] コマンドを使用して、クラスター内のノードを一覧表示します。 次の例に示されているように、*failure-domain.beta.kubernetes.io/zone* 値をフィルター処理します。

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

次の出力例では、指定のリージョンの複数の可用性ゾーンに分散した 3 つのノードが示されています (1 番目の可用性ゾーンは *eastus2-1*、2 番目の可用性ゾーンは *eastus2-2* など)。

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

エージェント プールにさらにノードを追加すると、Azure Platform は、基になる VM を指定の複数の可用性ゾーンに自動的に分散させます。

新しい Kubernetes バージョン (1.17.0 以降以降) では、AKS では非推奨の `failure-domain.beta.kubernetes.io/zone` に加えて新しいラベル `topology.kubernetes.io/zone` が使用されていることに注意してください。

## <a name="verify-pod-distribution-across-zones"></a>複数のゾーンへのポッドの分散を確認する

「[有名なラベル、注釈、およびテイント][kubectl-well_known_labels]」に記載されているように、Kubernetes は `failure-domain.beta.kubernetes.io/zone` ラベルを使用して、使用可能なさまざまなゾーンにわたってレプリケーション コントローラーまたはサービス内のポッドを自動的に分散します。 これをテストするには、クラスターを 3 ノードから 5 ノードにスケールアップして、正しいポッド拡散を確認します。

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

数分後にスケール操作が完了すると、コマンド `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` によって、次の例のような出力が得られます。

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

ご覧のとおり、ゾーン 1 とゾーン 2 に 2 つのノードが追加されました。 3 つのレプリカで構成されるアプリケーションをデプロイできます。 例として NGINX を使用します。

```console
kubectl run nginx --image=nginx --replicas=3
```

ポッドが実行されているノードを確認すると、3 つの異なる可用性ゾーンに対応するポッド上でポッドが実行されていることがわかります。 たとえば、コマンド `kubectl describe pod | grep -e "^Name:" -e "^Node:"` を使用すると、次のような出力が表示されます。

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

前の出力からわかるように、最初のポッドは、可用性ゾーン `eastus2-1` にあるノード 0 で実行されています。 2 つ目のポッドは、`eastus2-3` に対応するノード 2 で実行されています。また、3 つ目のポッドは、`eastus2-2` のノード 4 で実行されています。 追加の構成を行うことなく、Kubernetes は 3 つの可用性ゾーンすべてにわたってポッドを正しく分散します。

## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーンを使用する AKS クラスターの作成方法について詳しく説明します。 高可用性クラスターの考慮事項について詳しくは、「[AKS での事業継続とディザスター リカバリーに関するベスト プラクティス][best-practices-bc-dr]」をご覧ください。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
