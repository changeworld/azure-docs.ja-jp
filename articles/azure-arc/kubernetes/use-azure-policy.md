---
title: Azure Policy を使用してクラスター構成を大規模に適用する
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Azure Policy を使用してクラスター構成を大規模に適用する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: 9713f57207942c9fed5613b298ea83865ef685bd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059433"
---
# <a name="use-azure-policy-to-apply-gitops-configurations-at-scale"></a>Azure Policy を使用して GitOps 構成を大規模に適用する

Azure Policy を使用すると、Azure Arc 対応 Kubernetes クラスター (`Microsoft.Kubernetes/connectedclusters`) に構成 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations` のリソースの種類) を大規模に適用できます。

Azure Policy を使用するには、組み込みの GitOps ポリシー定義を選択して、ポリシーの割り当てを作成します。 ポリシー割り当てを作成する場合は、次の手順を実行します。
1. 割り当てのスコープを設定します。
    * スコープは、サブスクリプションまたは管理グループ内のすべてのリソース グループ、または特定のリソース グループになります。
2. 作成される GitOps 構成に対してパラメーターを設定します。 

割り当てが作成されると、Azure Policy エンジンによって、スコープ内にあるすべての Azure Arc 対応 Kubernetes クラスターが識別され、各クラスターに GitOps 構成が適用されます。

懸念事項を分離できるようにするために、複数のポリシー割り当てを作成し、それぞれに異なる GitOps 構成を使用して、異なる Git リポジトリを指すことができます。 たとえば、1 つのリポジトリがクラスター管理者によって使用され、他のリポジトリはアプリケーション チームによって使用される場合があります。

> [!TIP]
> このようなシナリオ用に、組み込みのポリシー定義があります。
> * Flux によって作成された SSH キーがあるパブリック リポジトリまたはプライベート リポジトリ: `Configure Kubernetes clusters with specified GitOps configuration using no secrets`
> * ユーザー指定の SSH キーがあるプライベート リポジトリ: `Configure Kubernetes clusters with specified GitOps configuration using SSH secrets`
> * ユーザー指定の HTTPS キーがあるプライベート リポジトリ: `Configure Kubernetes clusters with specified GitOps configuration using HTTPS secrets`

## <a name="prerequisite"></a>前提条件

このポリシー割り当てを作成するスコープ (サブスクリプションまたはリソース グループ) に対する `Microsoft.Authorization/policyAssignments/write` アクセス許可があることをご確認ください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

1. Azure portal で **[ポリシー]** に移動します。
1. サイドバーの **[作成]** セクションで **[定義]** を選択します。
1. "Kubernetes" カテゴリで、"シークレットを使用しないで、指定した GitOps 構成で Kubernetes クラスターを構成する" 組み込みポリシー定義を選択します。 
1. **[割り当て]** をクリックします。
1. **[スコープ]** を、ポリシー割り当てを適用する管理グループ、サブスクリプション、またはリソース グループに設定します。
    * ポリシー割り当てのスコープからリソースを除外する場合は、 **[除外]** を設定します。
1. ポリシー割り当てにわかりやすい **[名前]** と **[説明]** を指定します。
1. **[ポリシーの適用]** が "**有効**" に設定されていることを確認します。
1. **[次へ]** を選択します。
1. `sourceControlConfiguration` の作成時に使用するパラメーター値を設定します。
    * パラメーターの詳細については、[GitOps 構成のデプロイに関するチュートリアル](./tutorial-use-gitops-connected-cluster.md)を参照してください。
1. **[次へ]** を選択します。
1. **[修復タスクを作成する]** を有効にします。
1. **[マネージド ID を作成します]** がオンになっていること、および ID に **共同作成者** アクセス許可が付与されることを確認します。 
    * 詳細については、[ポリシー割り当ての作成のクイック スタート](../../governance/policy/assign-policy-portal.md)および [Azure Policy に準拠していないリソースの修復に関する記事](../../governance/policy/how-to/remediate-resources.md)を参照してください。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

ポリシー割り当てを作成すると、ポリシー割り当てのスコープ内で作成された新しい Azure Arc 対応 Kubernetes クラスターに構成が適用されます。

既存のクラスターに対しては、修復タスクを手動で実行する必要がある場合があります。 通常、このタスクには、ポリシー割り当てが有効になるまで 10 ～ 20 分かかります。

## <a name="verify-a-policy-assignment"></a>ポリシーの割り当てを確認する

1. Azure portal で、Azure Arc 対応 Kubernetes クラスターのいずれかに移動します。
1. サイドバーの **[設定]** セクションで、 **[ポリシー]** を選択します。 
    * 一覧に、前に作成した、 **[コンプライアンスの状態]** が *[準拠している]* に設定されているポリシーの割り当てが表示されます。
1. サイドバーの **[設定]** セクションで、 **[GitOps]** を選択します。
    * 構成の一覧に、ポリシー割り当てによって作成された構成が表示されます。
1. `kubectl` を使用してクラスターを問い合わせます。 
    * GitOps 構成によって作成された名前空間と成果物が表示されます。
    * Git リポジトリ内のマニフェストに記述されているオブジェクトがクラスターにデプロイされていることがわかるはずです。

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 Kubernetes クラスターを使用して Azure Monitor for Containers を設定します](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)。
