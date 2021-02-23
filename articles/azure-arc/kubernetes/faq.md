---
title: Azure Arc 対応 Kubernetes に関してよく寄せられる質問
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事には、Azure Arc 対応 Kubernetes に関してよく寄せられる質問の一覧が記載されています。
keywords: Kubernetes, Arc, Azure, コンテナー, 構成, GitOps, faq
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561242"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>よく寄せられる質問 - Azure Arc 対応 Kubernetes

この記事では、Azure Arc 対応 Kubernetes についてよく寄せられる質問を記載します。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Azure Arc 対応 Kubernetes と Azure Kubernetes Service (AKS) の違いは何ですか?

AKS は、Azure によるマネージド Kubernetes オファリングです。 AKS は、複雑さと運用上のオーバーヘッドの多くを Azure にオフロードすることで、Azure でのマネージド Kubernetes クラスターのデプロイを簡素化します。 Kubernetes マスターは Azure によって管理されるので、お客様はエージェント ノードの管理と保守のみを行います。

Azure Arc 対応 Kubernetes を使用すると、Kubernetes クラスターを Azure に接続することで、Azure の管理機能 (Azure Monitor や Azure Policy など) を拡張できます。 ユーザーは、基になる Kubernetes クラスター自体を管理します。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Azure で実行されている AKS クラスターを Azure Arc に接続する必要はありますか?

いいえ。 Azure Monitor や Azure Policy (Gatekeeper) を含む Azure Arc 対応 Kubernetes のすべての機能は、AKS (Azure Resource Manager のネイティブ リソース) 上で利用できます。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Azure Stack Hub と Azure Stack Edge 上の AKS-HCI クラスターと Kubernetes クラスターを Azure Arc に接続する必要がありますか?

はい。Azure Stack Edge または Azure Stack Hub 上の AKS-HCI クラスターまたは Kubernetes クラスターを Azure Arc に接続することで、Azure Resource Manager のリソース表現がクラスターに提供されます。 このリソース表現により、クラスター構成、Azure Monitor、Azure Policy (Gatekeeper) などの機能を、接続する Kubernetes クラスターに拡張できます。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>期限切れの Azure Arc 対応 Kubernetes リソースの処理方法は?

Azure Arc 対応 Kuberenetes に関連付けられているマネージド サービス ID (MSI) 証明書の有効期限は 90 日です。 この証明書の有効期限が切れると、リソースは `Expired` であると見なされ、このクラスターで構成、監視、ポリシーなどのすべての機能が停止します。 次の手順に従って、Kubernetes クラスターが Azure Arc で再び機能するようにしてください。

1. クラスター上の Azure Arc 対応 Kubernetes リソースとエージェントを削除します。 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. クラスターにエージェントを再デプロイして、Azure Arc 対応 Kubernetes リソースを再作成します。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` を使用すると、クラスター上の構成も削除されます。 `az connectedk8s connect` を実行した後に、手動で、または Azure Policy を使用して、クラスター上で構成を再作成します。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>CI/CD パイプラインを既に使用している場合でも、Azure Arc 対応 Kubernetes と構成を使用できますか?

はい。その場合でも CI/CD パイプラインを介してデプロイを受け取るクラスターで構成を使用できます。 従来の CI/CD パイプラインと比較すると、構成で 2 つの追加メリットが得られます。

**ドリフト調整**

CI/CD パイプラインでは、パイプラインの実行中に変更が 1 回だけ適用されます。 ただし、クラスター上の Kubernetes リソースの望ましい状態を取得するために、クラスター上の GitOps オペレーターによって Git リポジトリが継続的にポーリングされます。 GitOps オペレーターによって、リソースの望ましい状態がクラスター上のリソースの実際の状態とは異なることが検出されると、このドリフトが調整されます。

**GitOps が全体的に適用される**

CI/CD パイプラインは、Kubernetes クラスターへのイベント ドリブン デプロイに適しています。この場合、イベントを Git リポジトリにプッシュできます。 ただし、すべての Kubernetes クラスターに同じ構成をデプロイするには、これらの各 Kubernetes クラスターの資格情報を使用して CI/CD パイプラインを手動で構成する必要があります。 それに対し、Azure Arc 対応 Kubernetes の場合は、Azure Resource Manager によって構成が管理されるため、Azure Policy を使用することで、すべての Kubernetes クラスターに対する必要な構成の適用を、サブスクリプションまたはリソース グループのスコープのもとで一括して自動化できます。 この機能は、ポリシーの割り当て後に作成された Azure Arc 対応 Kubernetes リソースにも適用できます。

この構成機能は、コンプライアンスとガバナンスの要件を満たすために、Kubernetes クラスターのインベントリ全体に対するネットワーク ポリシー、ロール バインド、ポッド セキュリティ ポリシーなどのベースライン構成の適用に使用されます。

## <a name="next-steps"></a>次のステップ

* [Azure Arc にクラスターを接続する](./connect-cluster.md)
* [Arc 対応 Kubernetes クラスターの構成を作成する](./use-gitops-connected-cluster.md)
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)
