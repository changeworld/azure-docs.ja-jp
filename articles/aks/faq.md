---
title: Azure Kubernetes Service (AKS) についてよく寄せられる質問
description: Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 6bfcd11dd6bfd31583fb2d0cd3f4229d3dd70065
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65887365"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-currently-provide-aks"></a>現在はどの Azure リージョンで AKS が提供されていますか?

利用可能なリージョンの完全な一覧については、[AKS の利用可能なリージョン][aks-regions]に関するページを参照してください。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか?

はい、自動スケールは、Kubernetes 1.10 以降、[Kubernetes autoscaler][auto-scaler] 経由で使用できます。 クラスターの自動スケーラーを手動で構成して使用する方法の詳細については、[AKS のクラスター自動スケーリング][aks-cluster-autoscale]に関するページを参照してください。

ノードのスケーリングを管理するために、組み込みのクラスターの自動スケーラー (現在、AKS ではプレビュー中) を使用することもできます。 詳細については、[AKS でのアプリケーションの需要を満たすようにクラスターを自動的にスケーリング][aks-cluster-autoscaler]に関するページを参照してください。

## <a name="does-aks-support-kubernetes-rbac"></a>AKS では Kubernetes RBAC はサポートされていますか?

はい、Azure CLI でクラスターを作成すると、Kubernetes のロールベースのアクセス制御 (RBAC) が既定で有効になります。 Azure portal またはテンプレートを使って作成したクラスターで RBAC を有効にすることができます。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>既存の仮想ネットワークに AKS をデプロイできますか?

はい、[高度なネットワーク機能][aks-advanced-networking]を使用して、既存の仮想ネットワークに AKS クラスターをデプロイできます。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kubernetes API サーバーを仮想ネットワーク内でのみアクセスできるようにできますか?

現時点ではありません。 Kubernetes API サーバーは、パブリックの完全修飾ドメイン名 (FQDN) として公開されます。 クラスターへのアクセスは、[Kubernetes の RBAC と Azure Active Directory (Azure AD)][aks-rbac-aad] を使って制御できます。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

Azure では、セキュリティ更新プログラムが夜間スケジュールでご利用のクラスター内の Linux ノードに自動的に適用されます。 ただし、必要な場合は、それらの Linux ノードが確実に再起動されるようにする必要があります。 ノードを再起動するにはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターでは、[cordon ノードと drain ノード][cordon-drain]が自動的にアップグレードされてから、最新の Ubuntu イメージと、新しいパッチ バージョンまたは Kubernetes のマイナー バージョンで、新しいノードがオンラインにされます。 詳細については、「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください。
- Kubernetes 用のオープン ソースの再起動デーモンである [Kured](https://github.com/weaveworks/kured) を使用します。 Kured は [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) として実行され、再起動が必要であることを示すファイルの存在が各ノードで監視されます。 クラスター全体で、クラスターのアップグレードと同じ [cordon および drain プロセス][cordon-drain]によって OS の再起動が管理されます。

Kured の使用について詳しくは、[AKS 上のノードへのセキュリティおよびカーネルの更新プログラムの適用][node-updates-kured]に関する記事をご覧ください。

### <a name="windows-server-nodes"></a>Windows Server ノード

Windows Server ノードでは (現在、AKS ではプレビュー中)、Windows Update が自動的に実行されたり、最新の更新プログラムが適用されたりすることはありません。 Windows Update のリリース サイクルと独自の検証プロセス周辺の定期的スケジュールで、AKS クラスター内の Windows Server ノード プールでアップグレードを実行する必要があります。 このアップグレード プロセスでは、最新の Windows Server イメージと修正プログラムを実行するノードが作成されて、古いノードが削除されます。 このプロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

各 AKS デプロイは、2 つのリソース グループにまたがります。

1. 最初のリソース グループを作成します。 このグループには、Kubernetes サービスのリソースのみが含まれます。 AKS リソース プロバイダーにより、デプロイの間に 2 番目のリソース グループが自動的に作成されます。 2 番目のリソース グループの例は、*MC_myResourceGroup_myAKSCluster_eastus* です。 この 2 つ目のリソース グループの名前を指定する方法については、次のセクションをご覧ください。
1. *MC_myResourceGroup_myAKSCluster_eastus* などの 2 つ目のリソース グループには、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これらのリソースには、Kubernetes ノードの VM、仮想ネットワー キング、およびストレージが含まれます。 このリソース グループの目的は、リソースのクリーンアップを簡単にすることです。

ストレージ アカウントや予約済みパブリック IP アドレスなど、AKS クラスターで使用するリソースを作成する場合は、自動的に生成されたリソース グループにそれらを配置します。

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>AKS インフラストラクチャ リソース グループに独自の名前を指定できますか?

はい。 既定では、AKS リソース プロバイダーによってデプロイの間にセカンダリ リソース グループ (*MC_myResourceGroup_myAKSCluster_eastus* など) が自動的に作成されます。 企業ポリシーに準拠するために、この管理対象クラスター (*MC_* ) リソース グループに独自の名前を指定できます。

独自のリソース グループ名を指定するには、[aks-preview][aks-preview-cli] Azure CLI 拡張機能バージョン *0.3.2* 以降をインストールします。 [az aks create][az-aks-create] コマンドを使って AKS クラスターを作成するときに、 *--node-resource-group* パラメーターを使ってリソース グループの名前を指定します。 [Azure Resource Manager テンプレートを使って][aks-rm-template] AKS クラスターをデプロイする場合は、*nodeResourceGroup* プロパティを使ってリソース グループ名を定義できます。

* セカンダリ リソース グループは、自分のサブスクリプションの Azure リソース プロバイダーによって自動的に作成されます。
* カスタム リソース グループの名前を指定できるのは、クラスターを作成するときだけです。

*MC_* リソース グループを使うときは、次のことができないので注意してください。

* *MC_* グループに対して既存のリソース グループを指定すること。
* *MC_* リソース グループに対して異なるサブスクリプションを指定すること。
* クラスターが作成された後で、*MC_* リソース グループ名を変更すること。
* *MC_* リソース グループ内の管理対象リソースに名前を指定すること。
* *MC_* リソース グループ内の管理対象リソースのタグを変更または削除すること。 (詳しくは、次のセクションで追加の情報を参照してください。)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>MC_ リソース グループ内の AKS リソースのタグや他のプロパティを変更できますか?

*MC_* リソース グループ内の Azure で作成されたタグや他のリソース プロパティを変更または削除する場合、スケーリングやアップグレードのエラーなど、予期しない結果になる可能性があります。 AKS では、カスタム タグを作成および変更できます。 たとえばビジネス単位やコスト センターを割り当てるために、カスタム タグを作成または変更することがあります。 AKS クラスター内の *MC_* の下にあるリソースを変更すると、サービス レベル目標 (SLO) が中断されます。 詳細については、「[AKS でサービス レベル アグリーメントは提供されますか?](#does-aks-offer-a-service-level-agreement)」を参照してください。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS ではどのような Kubernetes アドミッション コントローラーがサポートされますか? アドミッション コントローラーの追加や削除はできますか?

AKS では、次の[アドミッション コントローラー][admission-controllers]をサポートします。

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

現在は、AKS でアドミッション コントローラーの一覧を変更することはできません。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

AKS は現在、Azure Key Vault とネイティブに統合されていません。 ただし、[Kubernetes プロジェクト用の Azure Key Vault FlexVolume][keyvault-flexvolume] を使って、Kubernetes ポッドから Key Vault のシークレットに直接統合することはできます。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

はい、Windows Server コンテナーはプレビューでご利用になれます。 AKS で Windows Server コンテナーを実行するには、Windows Server をゲスト OS として実行するノード プールを作成します。 Windows Server コンテナーでは、Windows Server 2019 のみを使用できます。 開始するには、[Windows Server ノード プールでの AKS クラスターの作成][aks-windows-cli]に関する記事をご覧ください。

Window Server によるノード プールのサポートには、Kubernetes プロジェクトの上流の Windows Server の一部であるいくつかの制限が含まれます。 これらの制限の詳細については、[AKS での Windows Server コンテナーの制限事項][aks-windows-limitations]に関するページを参照してください。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS でサービス レベル アグリーメントは提供されますか?

サービス レベル アグリーメント (SLA) では、プロバイダーは、公開されたサービス レベルが満たされない場合に、サービスの費用を顧客に払い戻すことに同意します。 AKS は無料であり、払い戻す費用がないため、AKS には正式な SLA はありません。 ただし、AKS では、Kubernetes API サーバーの 99.5 パーセント以上の可用性を維持できるようにしています。

## <a name="why-cant-i-set-maxpods-below-30"></a>maxPods を 30 未満に設定できないのはなぜですか?

AKS では、Azure CLI および Azure Resource Manager テンプレートを使ってクラスターを作成するときに、`maxPods` の値を設定することができます。 ただし、Kubenet と Azure CNI の両方で、"*最小限の値*" が必要です(作成時に検証されます)。

| ネットワーク | 最小値 | 最大値 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

AKS はマネージド サービスであるため、クラスターの一部としてアドオンとポッドをデプロイして管理します。 以前は、ユーザーはマネージド ポッドが実行するために必要な値より小さい値に `maxPods` を定義できました (たとえば 30)。 現在は、AKS では "((maxPods または (maxPods * vm_count)) > マネージド アドオン ポッドの最小値" という式を使って、ポッドの最小数が計算されるようになりました。

ユーザーは、最小の `maxPods` の検証をオーバーライドできません。

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
