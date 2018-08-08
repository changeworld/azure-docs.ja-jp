---
title: Azure Kubernetes Service についてよく寄せられる質問
description: Azure Kubernetes Service についてよく寄せられる質問にお答えします。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/27/2018
ms.author: iainfou
ms.openlocfilehash: b64c770bca84fba8cbed98e420abf649897f7a17
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345856"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>現在、Azure Kubernetes Service (AKS) は、どの Azure リージョンで提供されますか?

完全な一覧については、Azure Kubernetes Service の[リージョンと可用性][aks-regions]ドキュメントを参照してください。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

Azure では、セキュリティ更新プログラムが夜間スケジュールでクラスター内のノードに自動的に適用されます。 ただし、必要に応じてノードが再起動されることを確認する必要があります。 ノードの再起動の実行にはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターは [cordon および drain ノード](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)を自動的にアップグレードし、それらに最新の Ubuntu イメージのバックアップを適用します。 `az aks upgrade` で最新のクラスター バージョンを指定し、Kubernetes バージョンを変更せずにノードの OS イメージを更新します。
- Kubernetes 用のオープン ソースの再起動デーモンである [Kured](https://github.com/weaveworks/kured) を使用します。 Kured は [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) として実行され、再起動が必要であることを示すファイルの存在を各ノードで監視します。 さらに、前に説明したのと同じ cordon および drain プロセスに従って、クラスター間での OS の再起動を管理します。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか?

はい、自動スケールは、Kubernetes 1.10 以降、[Kubernetes autoscaler][auto-scaler] 経由で使用できます。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS では Kubernetes のロールベースのアクセス制御 (RBAC) がサポートされますか?

はい、[Azure CLI または Azure Resource Manager テンプレートから AKS クラスターをデプロイする](https://docs.microsoft.com/en-us/azure/aks/aad-integration)ときに、RBAC を有効にできます。 この機能は、まもなく Azure Portal でも使用可能になります。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS ではどのような Kubernetes アドミッション コントローラーがサポートされますか? アドミッション コントローラーの追加や削除はできますか?

AKS では、次の[アドミッション コントローラー][admission-controllers]をサポートします。

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

現時点では、AKS でアドミッション コントローラーの一覧を修正することはできません。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>既存の仮想ネットワークに AKS をデプロイできますか?

はい、[高度なネットワーク機能](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md)を使用して、既存の仮想ネットワークに AKS クラスターをデプロイできます。

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>仮想ネットワーク内でのみアクセスできるように Kubernetes API サーバーを制限できますか?

現時点ではありません。 Kubernetes API サーバーは、パブリックの完全修飾ドメイン名 (FQDN) として公開されます。 クラスターへのアクセスは、[Kubernetes のロールベースのアクセス制御 (RBAC) と Azure Active Directory (AAD)](https://docs.microsoft.com/en-us/azure/aks/aad-integration) 使って制御する必要があります。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

AKS は、現時点では、Azure Key Vault とネイティブに統合されていません。 ただし、 [KeyVault Flex Volume プロジェクト](https://github.com/Azure/kubernetes-keyvault-flexvol)を使用して、Kubernetes ポッドから KeyVault シークレットへの直接統合を行うことはできます。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

Windows Server コンテナーを実行するには、Windows Server ベースのノードを実行する必要があります。 Windows Server ベースのノードは、現時点では、AKS では使用できません。 ただし、Virtual Kubelet を使用して、Azure Container Instances で Windows コンテナーをスケジュールし、それらを AKS クラスターの一部として管理できます。 詳細については、[AKS での Virtual Kubelet の使用][virtual-kubelet]に関する記事を参照してください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

各 AKS デプロイは、2 つのリソース グループにまたがります。 1 つは自分が作成したリソース グループで、Kubernetes リソースのみが含まれます。 AKS リソース プロバイダーは、*MC_myResourceGroup_myAKSCluster_eastus* のような名前を持つ 2 つ目のリソース グループをデプロイ時に自動的に作成します。 2 つ目のリソース グループには、VM、ネットワーク、ストレージなど、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これはリソースのクリーンアップを簡略化するために作成されます。

AKS クラスターで使用するストレージ アカウントや予約済みパブリック IP アドレスなどのリソースを作成する場合は、自動的に生成されたリソース グループにそれらを配置する必要があります。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS でサービス レベル アグリーメントは提供されますか。

サービス レベル アグリーメント (SLA) では、公開されたサービス レベルを満たしていない場合に、プロバイダーがサービスの費用を顧客に払い戻すことに同意します。 AKS 自体は無料であるため、払い戻せる費用はありません。したがって、これは正式な SLA ではありません。 ただし、AKS では、Kubernetes API サーバーの 99.5% 以上の可用性を維持できるようにしています。

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
