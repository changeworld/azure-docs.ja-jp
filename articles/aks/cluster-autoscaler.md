---
title: Azure Kubernetes Service (AKS) でのクラスター オートスケーラーの使用
description: Azure Kubernetes Service (AKS) クラスターでのアプリケーションの需要を満たすように、クラスター オートスケーラーを使用してクラスターを自動的にスケーリングする方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 09610782f211b4cfb80a1291b73ab543328376a3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68424179"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でのアプリケーションの需要を満たすようにクラスターを自動的にスケーリングする

Azure Kubernetes Service (AKS) のアプリケーションの需要に対応するには、ワークロードを実行するノードの数を調整する必要が生じる場合があります。 クラスター オートスケーラー コンポーネントは、リソース制約のためにスケジュールできないクラスター内のポッドを監視できます。 問題が検出されると、アプリケーションの需要を満たすためにノード プール内のノードの数が増やされます。 また、実行ポッドの不足について定期的にノードがチェックされ、必要に応じてノードの数が減らされます。 AKS クラスター内のノードの数を自動的に増減するこの機能を使用すると、効率的で、コスト効率の高いクラスターを実行できます。

この記事では、AKS クラスターでクラスター オートスケーラーを有効にして管理する方法について説明します。 クラスター オートスケーラーはプレビューで AKS クラスターでのみテストする必要があります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

クラスター オートスケーラーを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.4 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールし、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>スケール セット機能プロバイダーを登録する

スケール セットを使用する AKS を作成するには、サブスクリプションで機能フラグを有効にする必要もあります。 *VMSSPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能の登録解除を行うことができません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>制限事項

クラスター オートスケーラーを使用する AKS クラスターを作成および管理する場合には、次の制限が適用されます。

* HTTP アプリケーションのルーティング アドオンは使用できません。

## <a name="about-the-cluster-autoscaler"></a>クラスター オートスケーラーについて

平日と夜間、または週末など、アプリケーションの変則的な需要に対応するために、多くの場合、クラスターには自動的にスケーリングする方法が必要になります。 AKS クラスターは、次の 2 つの方法のいずれかでスケーリングできます。

* **クラスター オートスケーラー**は、リソース制約のためにノードでスケジュールできないポッドを監視します。 状況に応じて、クラスターはノードの数を自動的に増やします。
* **ポッドの水平オートスケーラー**は、Kubernetes クラスターのメトリック サーバーを使用して、ポッドのリソースの需要をモニターします。 サービスに必要なリソース量が増えると、その需要を満たすためにポッドの数が自動的に増やされます。

![クラスター オートスケーラーとポッドの水平オートスケーラーは、必要なアプリケーション需要に対応するために、多くの場合、連携して機能します。](media/autoscaler/cluster-autoscaler.png)

また、ポッドの水平オートスケーラーとクラスター オートスケーラーは、必要に応じてポッドとノードの数を減らすこともできます。 クラスター オートスケーラーは、一定期間未使用の容量があった場合、ノードの数を減らします。 クラスター オートスケーラーによって削除されるノード上のポッドは、クラスター内の他の場所で安全にスケジュールされます。 クラスター オートスケーラーは、以下の状況のように、ポッドが移動できない場合はスケールダウンできないことがあります。

* ポッドが直接作成されており、デプロイやレプリカ セットなどのコントローラー オブジェクトによってサポートされていない。
* Pod Disruption Budget (PDB) の制限が非常に厳しく、ポッドの数が特定のしきい値を下回ることが許可されていない。
* ポッドが、別のノードでスケジュールされた場合に適用できないノード セレクターまたはアンチ アフィニティーを使用している。

クラスター オートスケーラーでスケールダウンを行えない場合がある状況について詳しくは、「[What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown]」 (クラスター オートスケーラーがノードから削除されるのを防止できるのは、どの種類のポッドですか?) を参照してください。

クラスター オートスケーラーは、スケール イベント間の時間間隔やリソースしきい値などに対して開始パラメーターを使用します。 これらのパラメーターは、Azure プラットフォームによって定義されており、現在公開されていないため、調整することはできません。 クラスター オートスケーラーが使用するパラメーターについて詳しくは、「[What are the cluster autoscaler parameters?][autoscaler-parameters]」 (クラスター オートスケーラー パラメーターとは何ですか?) を参照してください。

これらの 2 つのオートスケーラーは連携して機能し、両方のオートスケーラーが 1 つのクラスターにデプロイされることがよくあります。 この 2 つを組み合わせて使用する場合、ポッドの水平オートスケーラーの主な役割は、アプリケーションの需要を満たすために必要な数のポッドを実行することになります。 クラスター オートスケーラーの主な役割は、スケジュールされたポッドをサポートするために必要な数のノードを実行することになります。

> [!NOTE]
> クラスター オートスケーラーを使用する場合、手動スケーリングは無効になります。 必要なノード数は、クラスター オートスケーラーによって決定されるようにします。 クラスターを手動でスケーリングする場合は、[クラスター オートスケーラーを無効化](#disable-the-cluster-autoscaler)します。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS クラスターの作成とクラスター オートスケーラーの有効化

AKS クラスターを作成する必要がある場合は、[az aks create][az-aks-create] コマンドを使用します。 クラスターのノード プール上でクラスター オートスケーラーを有効にして構成するには、 *--enable-cluster-autoscaler* パラメーターを使用し、ノードの *--min-count* と *--max-count* を指定します。

> [!IMPORTANT]
> クラスター オートスケーラーは、Kubernetes のコンポーネントです。 AKS クラスターは、ノードに仮想マシン スケール セットを使用しますが、Azure portal で、または Azure CLI を使用して、スケール セットの自動スケーリングの設定を手動で有効にしたり編集したりしないでください。 必要なスケール設定の管理は、Kubernetes クラスター オートスケーラーが行います。 詳細については、[ノード リソース グループ内の AKS リソースを変更可能かどうか](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)に関するセクションを参照してください。

次の例では、仮想マシンのスケール セットを使用して AKS クラスターを作成します。 また、クラスターのノードプール上でクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定します。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> `az aks create` を実行するときに *--kubernetes-version* を指定した場合、そのバージョンは、前述の「[開始する前に](#before-you-begin)」セクションで概説されている必須の最小バージョン番号以上である必要があります。

このクラスターを作成して、クラスター オートスケーラーの設定を構成するには数分かかります。

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-an-aks-cluster"></a>AKS クラスター内の既存のノード プールでのクラスター オートスケーラーの有効化

前述の「[開始する前に](#before-you-begin)」セクションで概説されている要件を満たす、AKS クラスター内のノード プール上でクラスター オートスケーラーを有効にすることができます。 ノード プール上でクラスター オートスケーラーを有効にするには、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用します。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

上の例では *myAKSCluster* 内の *mynodepool* ノード プールのクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定しています。 最小ノード数がノード プール内の既存ノード数を超えている場合は、追加ノードを作成するために数分かかります。

## <a name="change-the-cluster-autoscaler-settings"></a>クラスター オートスケーラーの設定の変更

AKS クラスターを作成するか既存のノード プールを更新する前述のステップで、クラスター オートスケーラーの最小ノード数は *1* に設定され、最大ノード数は *3* に設定されました。 アプリケーション需要の変化に応じて、クラスター オートスケーラーのノード数の調整が必要になる場合があります。

ノード数を変更するには、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用します。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上の例では、 *myAKSCluster* の *mynodepool* ノード プールにあるクラスター オートスケーラーを更新し、最小値を *1* ノード、最大値を *5* ノードにします。

> [!NOTE]
> プレビュー中は、現在ノード プールに設定されている数より多い最小ノード数を設定することはできません。 例えば、現在、最小数が *1* に設定されている場合、最小数を *3* に更新することはできません。

アプリケーションとサービスのパフォーマンスをモニターして、必要なパフォーマンスに一致するようにクラスター オートスケーラーのノード数を調整してください。

## <a name="disable-the-cluster-autoscaler"></a>クラスター オートスケーラーの無効化

クラスター オートスケーラーを今後使用しない場合は、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用して、 *--disable-cluster-autoscaler* パラメーターを指定することで無効にできます。 クラスター オートスケーラーが無効になってもノードは削除されません。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

[az aks scale][az-aks-scale] コマンドを使用して、クラスターを手動でスケーリングできます。 ポッドの水平オートスケーラーを使用する場合、その機能はクラスター オートスケーラーを無効にしても維持されますが、ノード リソースがすべて使用中になると、ポッドをスケジュールできなくなる場合があります。

## <a name="next-steps"></a>次の手順

この記事では、AKS ノードの数を自動的にスケーリングする方法について説明します。 また、ポッドの水平オートスケーラーを使用して、アプリケーションを実行するポッドの数を自動的に調整することもできます。 ポッドの水平オートスケーラーの使用手順については、「[AKS でのアプリケーションのスケーリング][aks-scale-apps]」を参照してください。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
