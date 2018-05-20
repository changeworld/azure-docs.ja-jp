---
title: Azure Kubernetes Service についてよく寄せられる質問
description: Azure Kubernetes Service についてよく寄せられる質問にお答えします。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 55006a3f0193c96849c52f87ab01dc13ac0c7a16
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) は現在、**プレビュー**の段階です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>現在、Azure Kubernetes Service (AKS) は、どの Azure リージョンで提供されますか?

- カナダ中部
- カナダ東部
- 米国中央部
- 米国東部
- 東南アジア
- 西ヨーロッパ
- 米国西部 2

## <a name="when-will-additional-regions-be-added"></a>その他のリージョンが追加されるのはいつ頃になりますか?

その他のリージョンは、需要の増加に応じて追加されます。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

Azure では、セキュリティ更新プログラムが夜間スケジュールでクラスター内のノードに自動的に適用されます。 ただし、必要に応じてノードが再起動されることを確認する必要があります。 ノードの再起動の実行にはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターは [cordon および drain ノード](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)を自動的にアップグレードし、それらに最新の Ubuntu イメージのバックアップを適用します。 `az aks upgrade` で最新のクラスター バージョンを指定し、Kubernetes バージョンを変更せずにノードの OS イメージを更新します。
- Kubernetes 用のオープン ソースの再起動デーモンである [Kured](https://github.com/weaveworks/kured) を使用します。 Kured は [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) として実行され、再起動が必要であることを示すファイルの存在を各ノードで監視します。 さらに、前に説明したのと同じ cordon および drain プロセスに従って、これらの再起動をクラスター間で調整します。

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>ACS または AKS を使用することを Microsoft は推奨していますか?

AKS はプレビューに残っていますが、ACS Kubernetes または [acs-engine](https://github.com/azure/acs-engine) を使用して運用環境クラスターを作成することをお勧めします。 概念実証のデプロイや、開発/テスト環境では AKS を使用します。

## <a name="when-will-acs-be-deprecated"></a>ACS が非推奨化される時期を教えてください。

ACS は、AKS が GA になるころに非推奨化されます。 その日付から、クラスターを AKS に移行するための期間として 12 か月が設けられます。 この 12 か月間は、ACS のすべての操作を実行することができます。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか?

ノードの自動スケールはサポートされていませんが、ロードマップには含まれています。 オープン ソースによる[自動スケールの実装][auto-scaler]をご覧ください。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS では Kubernetes のロールベースのアクセス制御 (RBAC) がサポートされますか?

いいえ、RBAC は現在 AKS でサポートされていませんが、間もなく使用できるようになります。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>既存の仮想ネットワークに AKS をデプロイできますか?

いいえ、これはまだありませんが、間もなく使用できるようになります。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

いいえ。ただし、統合する計画はあります。 その間は、[Hexadite][hexadite] から提供されている次のソリューションをお試しください。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

いいえ、AKS は現在 Windows Server ベースのエージェント ノードを提供していないため、Windows Server コンテナーを実行することはできません。 Azure の Kubernetes で Windows Server コンテナーを実行する必要がある場合は、[acs-engine のドキュメント](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)をご覧ください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

各 AKS デプロイは、2 つのリソース グループにまたがります。 1 つは自分が作成したリソース グループで、AKS リソースのみが含まれます。 AKS リソース プロバイダーは、*MC_myResourceGRoup_myAKSCluster_eastus* のような名前を持つ 2 つ目のリソース グループをデプロイ時に自動的に作成します。 2 つ目のリソース グループには、VM、ネットワーク、ストレージなど、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これはリソースのクリーンアップを簡略化するために作成されます。

AKS クラスターで使用するストレージ アカウントや予約済みパブリック IP アドレスなどのリソースを作成する場合は、自動的に生成されたリソース グループにそれらを配置する必要があります。

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent