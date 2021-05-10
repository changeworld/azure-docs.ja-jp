---
title: Azure Kubernetes Service (AKS) に移行する
description: Azure Kubernetes Service (AKS) に移行します。
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: d5e3543fd6b7cd1b5534d6e363e51f1778cc7fc9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012129"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) に移行する

Azure Kubernetes Service (AKS) への正常な移行を計画して実行できるように、このガイドでは、現在お勧めの AKS 構成の詳細について説明します。 この記事では、すべてのシナリオについて説明しているわけではなく、移行が成功するように計画するための詳細情報へのリンクが示されています。

このドキュメントでは、次のシナリオをサポートできます。

* [Azure Migrate](../migrate/migrate-services-overview.md) を使用した特定のアプリケーションのコンテナー化と AKS へのそれらの移行。
* [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)によってサポートされている AKS クラスターの [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) への移行。
* [Standard SKU ロードバランサー](./load-balancer-standard.md)の使用への AKS クラスターの移行。
* [2020 年 1 月 31 日に廃止される Azure Container Service (ACS)](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) から AKS への移行。
* [AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)から AKS への移行。
* Azure 以外に基づく Kubernetes クラスターから AKS への移行。
* 既存のリソースを別のリージョンに移動。

移行するときは、ターゲットの Kubernetes のバージョンが AKS に対して確実にサポート対象期間内であるようにします。 古いバージョンはサポート対象範囲内ではない場合があり、AKS でサポートされるバージョンのアップグレードが必要になります。 詳細については、[AKS でサポートされる Kubernetes のバージョン](./supported-kubernetes-versions.md)に関するページを参照してください。

新しいバージョンの Kubernetes に移行する場合は、「[Kubernetes のバージョンとバージョン スキュー サポート ポリシー](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)」を確認してください。

シナリオによっては、いくつかのオープンソース ツールが移行に役立つ場合があります。

* [Velero](https://velero.io/) (Kubernetes 1.7 以降が必要)
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

この記事では、次の移行の詳細について説明します。

> [!div class="checklist"]
> * Azure Migrate によるアプリケーションのコンテナー化 
> * Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS
> * 既存の接続されている Azure サービス
> * 有効なクォータを確保する
> * 高可用性とビジネス継続性
> * ステートレス アプリケーションに関する考慮事項
> * ステートフル アプリケーションに関する考慮事項
> * クラスター構成のデプロイ

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Azure Migrate によるアプリケーションの AKS への移行

Azure Migrate により、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データの評価と、Azure へのそれらの移行を行うための統合プラットフォームが提供されます。 AKS には、次のタスクで Azure Migrate を使用できます。

* [ASP.NET アプリケーションのコンテナー化と AKS へのそれらの移行](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Java Web アプリケーションのコンテナー化と AKS へのそれらの移行](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS

AKS は、管理オーバーヘッドが少ない独自の機能を提供するマネージド サービスです。 AKS はマネージド サービスであるため、AKS でサポートされている一連の[リージョン](./quotas-skus-regions.md)から選択する必要があります。 既存のクラスターから AKS への移行中に、AKS のマネージド コントロール プレーンで正常性が維持されるよう、既存のアプリケーションの変更が必要になる場合があります。

次のような機能を確実に利用できるように、[Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) および [Azure Standard Load Balancer](./load-balancer-standard.md) でサポートされる AKS クラスターを使用することをお勧めします。
* [複数のノード プール](./use-multiple-node-pools.md)、
* [Availability Zones](../availability-zones/az-overview.md)、
* [認可された IP 範囲](./api-server-authorized-ip-ranges.md)、
* [クラスター オートスケーラー](./cluster-autoscaler.md)、
* [AKS 用 Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md)、
* その他の新機能 (リリースされたとき)。

[仮想マシン可用性セット](../virtual-machines/availability.md#availability-sets)によって支援された AKS クラスターでは、これらの機能の多くがサポートされません。

次の例では、仮想マシン (VM) スケール セットによってサポートされる 1 つのノード プールを使用して、AKS クラスターを作成します。 クラスターは次のようになります。
* Standard Load Balancer を使用します。 
* クラスターのノード プール上でクラスター オートスケーラーを有効にします。
* 最小 *1* つ、最大 *3* つのノードを設定します。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>既存の接続されている Azure サービス

クラスターを移行するとき、外部の Azure サービスに接続していることがあります。 次のサービスではリソースを再作成する必要はありませんが、機能を維持するには、以前のクラスターから新しいクラスターに接続を更新する必要があります。

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* ストレージ アカウント
* 外部データベース

## <a name="ensure-valid-quotas"></a>有効なクォータを確保する

移行中にその他の VM がサブスクリプションにデプロイされるため、クォータと制限がこれらのリソースに十分であることを確認する必要があります。 必要に応じて、[vCPU クォータ](../azure-portal/supportability/per-vm-quota-requests.md)の増量を要求します。

IP を使い切らないようにするには、[ネットワーク クォータ](../azure-portal/supportability/networking-quota-requests.md)の増量を要求することが必要になる場合があります。 詳細については、[AKS のネットワークと IP 範囲](./configure-kubenet.md)に関するページを参照してください。

詳細については、[Azure サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md) に関する記事を参照してください。 現在のクォータを確認するには、Azure portal で [サブスクリプション ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して **[使用量 + クォータ]** を選択します。

## <a name="high-availability-and-business-continuity"></a>高可用性とビジネス継続性

アプリケーションでダウンタイムを処理できない場合は、高可用性の移行シナリオに関するベスト プラクティスに従う必要があります。 [Azure Kubernetes Service (AKS) での複雑な事業継続の計画、ディザスター リカバリー、アップタイムの最大化に関するベスト プラクティス](./operator-best-practices-multi-region.md)に関するページを参照してください。

複雑なアプリケーションの場合、通常は、一度にすべてではなく時間をかけて移行します。つまり、古い環境と新しい環境では、ネットワークを介して通信する必要があります。 これまで `ClusterIP` サービスを使用して通信していたアプリケーションは、`LoadBalancer` タイプとして公開し、適切にセキュリティで保護することが必要な場合があります。

移行を完了するには、AKS で実行されている新しいサービスをクライアントで参照します。 AKS クラスターの前面に配置された Load Balancer をポイントするように DNS を更新して、トラフィックをリダイレクトすることをお勧めします。

[Azure Traffic Manager](../traffic-manager/index.yml) を使うと、お客様を目的の Kubernetes クラスターおよびアプリケーション インスタンスに送ることができます。 Traffic Manager は、ネットワーク トラフィックをリージョン間で分散することができる、DNS ベースのトラフィック ロード バランサーです。 パフォーマンスと冗長性を最大限に高めるため、すべてのアプリケーション トラフィックは、Traffic Manager を介して AKS クラスターへ送るようにします。 

複数クラスターの環境では、各 AKS クラスター上のサービスを指す、Traffic Manager DNS 名に接続するようにしましょう。 これらのサービスは、Traffic Manager エンドポイントを使用して定義します。 各エンドポイントは、"*サービス ロード バランサー IP*" です。 この構成を使用して、あるリージョンの Traffic Manager エンドポイントから別のリージョンのエンドポイントにネットワーク トラフィックを送信します。

![Traffic Manager を使用する AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) は、AKS クラスターのトラフィックをルーティングするためのもう 1 つのオプションです。 Azure Front Door Service では、高可用性のために最大限のパフォーマンスと即時グローバル フェイルオーバーを最適化することで、Web トラフィックのグローバル ルーティングを定義、管理、監視することができます。 

### <a name="considerations-for-stateless-applications"></a>ステートレス アプリケーションに関する考慮事項

ステートレス アプリケーションの移行は、最も簡単なケースです。
1. リソース定義 (YAML または Helm) を新しいクラスターに適用します。
1. すべてが想定どおりに動作していることを確認します。
1. トラフィックをリダイレクトして新しいクラスターをアクティブにします。

### <a name="considerations-for-stateful-applications"></a>ステートフル アプリケーションに関する考慮事項

データの損失や予期しないダウンタイムを回避するために、ステートフル アプリケーションの移行は慎重に計画してください。

* Azure Files を使用する場合は、ファイル共有をボリュームとして新しいクラスターにマウントできます。 [静的 Azure Files をボリュームとしてマウントする](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)に関するページを参照してください。
* Azure Managed Disks を使用するときは、どの VM にも接続されていない場合にのみ、ディスクをマウントできます。 [静的 Azure Disk をボリュームとしてマウントする](./azure-disk-volume.md#mount-disk-as-volume)に関するページを参照してください。
* どちらの方法も機能しない場合は、バックアップと復元のオプションを使用できます。 [Azure での Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)に関するページを参照してください。

#### <a name="azure-files"></a>Azure Files

ディスクとは異なり、Azure Files は複数のホストに同時にマウントできます。 AKS クラスターでは、Azure と Kubernetes によって、AKS クラスターでまだ使用されているポッドの作成は防止されません。 データの損失や予期しない動作を防ぐために、クラスターが同じファイルに同時に書き込まないようにしてください。

お使いのアプリケーションが、同じファイル共有を指す複数のレプリカをホストできる場合は、ステートレスの移行手順に従って、YAML 定義を新しいクラスターにデプロイしてください。 

ホストできない場合に可能な 1 つの移行方法は、次の手順です。

1. アプリケーションが正常に動作していることを確認します。
1. ライブ トラフィックを新しい AKS クラスターに送ります。
1. 古いクラスターを切断します。

共有を空にして開始し、ソース データのコピーを作成したい場合は、[`az storage file copy`](/cli/azure/storage/file/copy) コマンドを使用してデータを移行できます。


#### <a name="migrating-persistent-volumes"></a>永続ボリュームの移行

既存の永続ボリュームを AKS に移行する場合は、通常、次の手順に従います。

1. アプリケーションの書き込みを停止します。 
    * この手順は省略可能であり、ダウンタイムが必要になります。
1. ディスクのスナップショットを作成します。
1. スナップショットから新しいマネージド ディスクを作成します。
1. AKS に永続ボリュームを作成します。
1. PersistentVolumeClaims (静的プロビジョニング) ではなく[既存のボリュームを使用する](./azure-disk-volume.md)ようにポッドの指定を更新します。
1. アプリケーションを AKS にデプロイします。
1. アプリケーションが正常に動作していることを確認します。
1. ライブ トラフィックを新しい AKS クラスターに送ります。

> [!IMPORTANT]
> 書き込みを停止しないことを選択した場合は、データを新しいデプロイにレプリケートする必要があります。 そうしないと、ディスクのスナップショットを作成した後に書き込まれたデータが消失します。

マネージド ディスクを作成して Kubernetes クラスター間でのボリュームの移行に役立つオープン ソースのツールが存在します。

* [Azure CLI ディスク コピー拡張機能](https://github.com/noelbundick/azure-cli-disk-copy-extension)は、リソース グループ間および Azure リージョン間でディスクをコピーおよび変換します。
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)は、ACS Kubernetes ボリュームを列挙し、それらを AKS クラスターに移行します。


### <a name="deployment-of-your-cluster-configuration"></a>クラスター構成のデプロイ

既存の継続的インテグレーション (CI) および継続的配置 (CD) パイプラインを使用して、既知の正常な構成を AKS にデプロイすることをお勧めします。 Azure Pipelines を使用すると、[アプリケーションをビルドして AKS にデプロイ](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)できます。 既存のデプロイ タスクを複製し、`kubeconfig` が新しい AKS クラスターを指すようにします。

それが不可能な場合は、既存の Kubernetes クラスターからリソース定義をエクスポートし、AKS にそれを適用します。 オブジェクトをエクスポートするには `kubectl` を使用できます。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>既存のリソースを別のリージョンに移動する

AKS クラスターを、[AKS でサポートされている別のリージョン][region-availability]に移動する必要がある場合があります。 もう一方のリージョンに新しいクラスターを作成し、その新しいクラスターにリソースとアプリケーションをデプロイすることをお勧めします。 

また、AKS クラスターで実行されている [Azure Dev Spaces][azure-dev-spaces] などのサービスがある場合は、新しいリージョンのクラスターにこれらのサービスをインストールして構成する必要があります。


この記事では、次の移行の詳細について説明しました。

> [!div class="checklist"]
> * Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS
> * 既存の接続されている Azure サービス
> * 有効なクォータを確保する
> * 高可用性とビジネス継続性
> * ステートレス アプリケーションに関する考慮事項
> * ステートフル アプリケーションに関する考慮事項
> * クラスター構成のデプロイ


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
