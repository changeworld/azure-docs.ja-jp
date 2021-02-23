---
title: 構成と GitOps - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes の GitOps と構成の機能の概念的な概要を示します。
keywords: Kubernetes, Arc, Azure, コンテナー, 構成, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561233"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes に関する構成と GitOps

Kubernetes に関する場合、GitOps は、Git リポジトリで Kubernetes クラスター構成 (デプロイ、名前空間など) の望ましい状態を宣言する方法です。 この宣言の後に、オペレーターを使用してこれらのクラスター構成をポーリングし、プルベースのデプロイを実行します。 Git リポジトリには以下を含めることができます。
* Namespace、ConfigMap、Deployment、DaemonSet などの有効な Kubernetes リソースを記述する YAML 形式のマニフェスト。
* アプリケーションをデプロイするための Helm チャート。

GitOps 空間の一般的なオープンソース ツールである [Flux](https://docs.fluxcd.io/) は、Git リポジトリから Kubernetes クラスターへの構成の流れを容易にするために Kubernetes クラスターにデプロイできます。 Flux では、クラスターと名前空間の両方のスコープで、そのオペレーターのデプロイがサポートされます。 名前空間スコープでデプロイされた flux オペレーターの場合、その特定の名前空間内の Kubernetes オブジェクトのみをデプロイできます。 クラスターか名前空間のどちらかのスコープを選択できることで、同じ Kubernetes クラスターでのマルチテナント デプロイ パターンを実現できます。

## <a name="configurations"></a>構成

[ ![構成のアーキテクチャ](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

クラスターと Git リポジトリ間の接続は、Azure Resource Manager で Azure Arc 対応 Kubernetes リソース (`Microsoft.Kubernetes/connectedClusters` によって表されます) 上に `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 拡張リソースとして作成されます。 

`sourceControlConfiguration` リソース プロパティを使用して、適切なパラメーター (マニフェストのプル元である Git リポジトリや、それらをプルするポーリング間隔など) を使用してクラスターに Flux オペレーターをデプロイします。 `sourceControlConfiguration` データが保存される際は Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。

クラスターで実行されている `config-agent` は、次の役割を担います。
* Azure Arc 対応 Kubernetes リソースで、新規または更新された `sourceControlConfiguration` 拡張リソースを追跡する。
* それぞれの `sourceControlConfiguration` について Flux オペレーターをデプロイして Git リポジトリを監視する。
* `sourceControlConfiguration` に対して行われた更新を適用する。 

複数の名前空間スコープの `sourceControlConfiguration` リソースを同じ Azure Arc 対応 Kubernetes クラスター上に作成し、マルチテナントを実現できます。

> [!NOTE]
> * `config-agent` は Azure Arc 対応 Kubernetes リソース上で新規または更新された `sourceControlConfiguration` 拡張リソースが使用可能になったか監視するので、エージェントには、望ましい状態をクラスターにプルするための接続が必要です。 エージェントが Azure に接続できない場合、Azure Resource Manager の `sourceControlConfiguration` リソースで宣言された望ましい状態のプロパティはクラスターに適用されません。
> * 秘密キー、既知のホストのコンテンツ、HTTPS ユーザー名、トークン/パスワードなどの機密性の高い顧客入力は、Azure Arc 対応 Kubernetes サービスでは 48 時間以上保存されません。 構成で機密性の高い入力を使用している場合は、クラスターをできるだけ一定の間隔でオンラインにすることをお勧めします。

## <a name="apply-configurations-at-scale"></a>構成を大規模に適用する

Azure Resource Manager によって構成が管理されるので、Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープ内のすべての Azure Arc 対応 Kubernetes リソースで同じ構成を自動的に作成できます。 

このように大規模に適用することで、共通のベースライン構成 (ClusterRoleBindings、RoleBinding、NetworkPolicy などの構成を含む) を、Azure Arc 対応 Kubernetes クラスターのインベントリ全体に適用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Arc にクラスターを接続する](./connect-cluster.md)
* [Arc 対応 Kubernetes クラスターで構成を作成する](./use-gitops-connected-cluster.md)
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)