---
title: Azure Kubernetes Service (AKS) に移行する
description: Azure Kubernetes Service (AKS) に移行します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 8070e209910425f9baa0ae81aca349a067c70f76
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658544"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) に移行する

この記事では、Azure Kubernetes Service (AKS) への移行を成功させるための計画と実行について説明します。 このガイドでは、重要な決定に役立つよう、AKS に対して現在推奨されている構成について詳しく説明します。 この記事では、すべてのシナリオについて説明しているわけではなく、必要に応じて、移行が成功するように計画するための詳細情報へのリンクが示されています。

このドキュメントは、次のシナリオをサポートするために使用できます。

* [可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)によってサポートされている AKS クラスターの [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) への移行
* [Standard SKU ロードバランサー](https://docs.microsoft.com/azure/aks/load-balancer-standard)の使用への AKS クラスターの移行
* [2020 年 1 月 31 日に廃止される Azure Container Service (ACS)](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) から AKS への移行
* [AKS エンジン](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)から AKS への移行
* Azure 以外に基づく Kubernetes クラスターから AKS への移行

移行するときは、ターゲットの Kubernetes のバージョンが AKS に対して確実にサポート対象期間内であるようにします。 古いバージョンを使用している場合は、サポート対象範囲内ではなく、AKS でサポートされるようにバージョンをアップグレードすることが必要になる場合があります。 詳細については、[AKS でサポートされる Kubernetes のバージョン](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)に関する記事を参照してください。

新しいバージョンの Kubernetes に移行する場合は、「[Kubernetes のバージョンとバージョン スキュー サポート ポリシー](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)」を確認してください。

シナリオによっては、いくつかのオープンソース ツールが移行に役立つ場合があります。

* [Velero](https://velero.io/) (Kubernetes 1.7 以降が必要)
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

この記事では、次の移行の詳細について説明します。

> [!div class="checklist"]
> * Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS
> * 既存の接続されている Azure サービス
> * 有効なクォータを確保する
> * 高可用性とビジネス継続性
> * ステートレス アプリケーションに関する考慮事項
> * ステートフル アプリケーションに関する考慮事項
> * クラスター構成のデプロイ

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS

AKS は、管理オーバーヘッドが少ない独自の機能を提供するマネージド サービスです。 マネージド サービスであるため、AKS でサポートされている一連の[リージョン](https://docs.microsoft.com/azure/aks/quotas-skus-regions)から選択する必要があります。 既存のクラスターから AKS への移行では、AKS のマネージド コントロール プレーンでも正常性が維持されるよう、既存のアプリケーションの変更が必要になる場合があります。

[複数のノード プール](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)、[Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview)、[承認済み IP 範囲](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)、[クラスター オートスケーラー](https://docs.microsoft.com/azure/aks/cluster-autoscaler)、[AKS 用の Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)、およびリリースされたその他の新機能などをそのまま確実に利用できるように、[Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) および [Azure Standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) によって支援された AKS クラスターを使用することをお勧めします。   

[仮想マシン可用性セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)によって支援された AKS クラスターでは、これらの機能の多くがサポートされません。

次の例では、仮想マシンのスケール セットによって戻された 1 つのノード プールを使用して、AKS クラスターを作成します。 これは、Standard Load Balancer を使用しています。 また、クラスターのノードプール上でクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定します。

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

クラスターを移行するとき、外部の Azure サービスに接続していることがあります。 これらのリソースを再作成する必要はありませんが、機能を維持するには、以前のクラスターから新しいクラスターに接続を更新する必要があります。

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* ストレージ アカウント
* 外部データベース

## <a name="ensure-valid-quotas"></a>有効なクォータを確保する

移行中に追加の仮想マシンがサブスクリプションに展開されるため、クォータと制限がこれらのリソースに十分であることを確認する必要があります。 [vCPU クォータ](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)の増量を要求することが必要になる場合があります。

IP を使い切らないようにするには、[ネットワーク クォータ](https://docs.microsoft.com/azure/azure-supportability/networking-quota-requests)の増量を要求することが必要になる場合があります。 詳細については、[AKS のネットワークと IP 範囲](https://docs.microsoft.com/azure/aks/configure-kubenet)に関するページを参照してください。

詳細については、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) に関する記事を参照してください。 現在のクォータを確認するには、Azure portal で[サブスクリプション ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して **[使用量 + クォータ]** を選択します。

## <a name="high-availability-and-business-continuity"></a>高可用性とビジネス継続性

アプリケーションでダウンタイムを処理できない場合は、高可用性の移行シナリオに関するベスト プラクティスに従う必要があります。  複雑なビジネス継続性の計画、ディザスター リカバリー、アップタイムの最大化に関するベスト プラクティスについては、このドキュメントでは扱いません。  詳細については、「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)」を参照してください。

複雑なアプリケーションの場合は、通常、一度にではなく時間をかけて段階的に移行します。 つまり、古い環境と新しい環境が、ネットワークを介して通信する必要がある可能性があります。 これまで `ClusterIP` サービスを使用して通信していたアプリケーションは、`LoadBalancer` タイプとして公開し、適切にセキュリティで保護することが必要な場合があります。

移行を完了するには、AKS で実行されている新しいサービスをクライアントで参照します。 AKS クラスターの前面に配置された Load Balancer を指すように DNS を更新して、トラフィックをリダイレクトすることをお勧めします。

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) を使うと、お客様を目的の Kubernetes クラスターおよびアプリケーション インスタンスに送ることができます。  Traffic Manager は、ネットワーク トラフィックをリージョン間で分散することができる、DNS ベースのトラフィック ロード バランサーです。  パフォーマンスと冗長性を最大限に高めるため、すべてのアプリケーション トラフィックは、Traffic Manager を介して AKS クラスターへ送るようにします。  複数クラスター デプロイでは、お客様は、各 AKS クラスター上のサービスを指す、Traffic Manager の DNS 名に接続する必要があります。 これらのサービスは、Traffic Manager エンドポイントを使用して定義します。 各エンドポイントは、"*サービス ロード バランサー IP*" です。 この構成を使用して、あるリージョンの Traffic Manager エンドポイントから別のリージョンのエンドポイントにネットワーク トラフィックを送信します。

![Traffic Manager を使用する AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) は、AKS クラスターのトラフィックをルーティングするためのもう 1 つのオプションです。  Azure Front Door Service では、高可用性のために最大限のパフォーマンスと即時グローバル フェイルオーバーを最適化することで、Web トラフィックのグローバル ルーティングを定義、管理、および監視することができます。 

### <a name="considerations-for-stateless-applications"></a>ステートレス アプリケーションに関する考慮事項 

ステートレス アプリケーションの移行は、最も簡単なケースです。 リソース定義 (YAML または Helm) を新しいクラスターに適用し、すべてが想定どおりに動作していることを確認してから、トラフィックをリダイレクトして新しいクラスターをアクティブにします。

### <a name="considers-for-stateful-applications"></a>ステートフル アプリケーションに関する考慮事項

データの損失や予期しないダウンタイムを回避するために、ステートフル アプリケーションの移行は慎重に計画してください。

Azure Files を使用する場合は、ファイル共有をボリュームとして新しいクラスターにマウントできます。
* [静的 Azure Files をボリュームとしてマウントする](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Azure Managed Disks を使用する場合は、どの VM にも接続されていない場合にのみ、ディスクをマウントできます。
* [静的 Azure Disk をボリュームとしてマウントする](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

どちらの方法も機能しない場合は、バックアップと復元のオプションを使用できます。
* [Azure での Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

ディスクとは異なり、Azure Files は複数のホストに同時にマウントできます。 AKS クラスターで、Azure と Kubernetes は、ACS クラスターでまだ使用されているポッドが作成されるのを阻止しません。 データの損失や予期しない動作を防ぐために、クラスターが同じファイルに同時に書き込まないようにしてください。

お使いのアプリケーションが、同じファイル共有を指す複数のレプリカをホストできる場合は、ステートレスの移行手順に従って、YAML 定義を新しいクラスターにデプロイしてください。 ホストできない場合に可能な 1 つの移行方法は、次の手順です。

* アプリケーションが正常に動作していることを確認します。
* ライブ トラフィックを新しい AKS クラスターに送ります。
* 古いクラスターを切断します。

共有を空にして開始し、ソース データのコピーを作成したい場合は、[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) コマンドを使用してデータを移行できます。


#### <a name="migrating-persistent-volumes"></a>永続ボリュームの移行

既存の永続ボリュームを AKS に移行する場合は、通常、次の手順に従います。

* アプリケーションの書き込みを停止します。 (この手順は省略可能であり、ダウンタイムが必要になります。)
* ディスクのスナップショットを作成します。
* スナップショットから新しいマネージド ディスクを作成します。
* AKS に永続ボリュームを作成します。
* PersistentVolumeClaims (静的プロビジョニング) ではなく[既存のボリュームを使用する](https://docs.microsoft.com/azure/aks/azure-disk-volume)ようにポッドの指定を更新します。
* アプリケーションを AKS にデプロイします。
* アプリケーションが正常に動作していることを確認します。
* ライブ トラフィックを新しい AKS クラスターに送ります。

> [!IMPORTANT]
> 書き込みを停止しないことを選択した場合は、データを新しいデプロイにレプリケートする必要があります。 そうしないと、ディスクのスナップショットを作成した後に書き込まれたデータが消失します。

マネージド ディスクを作成して Kubernetes クラスター間でのボリュームの移行に役立つオープン ソースのツールが存在します。

* [Azure CLI ディスク コピー拡張機能](https://github.com/noelbundick/azure-cli-disk-copy-extension)は、リソース グループ間および Azure リージョン間でディスクをコピーおよび変換します。
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)は、ACS Kubernetes ボリュームを列挙し、それらを AKS クラスターに移行します。


### <a name="deployment-of-your-cluster-configuration"></a>クラスター構成のデプロイ

既存の継続的インテグレーション (CI) および継続的配置 (CD) パイプラインを使用して、既知の正常な構成を AKS にデプロイすることをお勧めします。 Azure Pipelines を使用して、[アプリケーションを構築し、AKS にデプロイする](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)ことができます。既存のデプロイ タスクを複製して、`kubeconfig` が確実に新しい AKS クラスターを指すようにします。

それが不可能な場合は、既存の Kubernetes クラスターからリソース定義をエクスポートし、AKS にそれを適用します。 オブジェクトをエクスポートするには `kubectl` を使用できます。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

この記事では、次の移行の詳細について説明しました。

> [!div class="checklist"]
> * Standard Load Balancer と Virtual Machine Scale Sets を使用する AKS
> * 既存の接続されている Azure サービス
> * 有効なクォータを確保する
> * 高可用性とビジネス継続性
> * ステートレス アプリケーションに関する考慮事項
> * ステートフル アプリケーションに関する考慮事項
> * クラスター構成のデプロイ
