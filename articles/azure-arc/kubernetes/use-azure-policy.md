---
title: Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy を使用してクラスター構成を大規模に適用する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: b80e50cb4823632f054de3b7f9da71392f8578d7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560193"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)

## <a name="overview"></a>概要

Azure Policy を使用すると、次のリソースのいずれかに特定の `Microsoft.KubernetesConfiguration/sourceControlConfigurations` が適用されるように強制することができます。
*  `Microsoft.Kubernetes/connectedclusters` リソース。
* GitOps 対応の `Microsoft.ContainerService/managedClusters` リソース。 

Azure Policy を使用するには、既存のポリシー定義を選択して、ポリシーの割り当てを作成します。 ポリシー割り当てを作成する場合は、次の手順を実行します。
1. 割り当てのスコープを設定します。
    * スコープは、Azure リソース グループまたはサブスクリプションになります。 
2. 作成される `sourceControlConfiguration` のパラメーターを設定します。 

割り当てが作成されると、Azure Policy エンジンでスコープ内のすべての `connectedCluster` または `managedCluster` リソースが識別され、それぞれに対して `sourceControlConfiguration` が適用されます。

複数のポリシー割り当てを使用して、各クラスターの真実のソースとして複数の Git リポジトリを有効にすることができます。 各ポリシー割り当ては、別の Git リポジトリを使用するように構成されます。たとえば、中央の IT/クラスター オペレーター用の 1 つのリポジトリと、アプリケーション チーム用のその他のリポジトリです。

## <a name="prerequisite"></a>前提条件

このポリシー割り当てを作成するスコープ (サブスクリプションまたはリソース グループ) に対する `Microsoft.Authorization/policyAssignments/write` アクセス許可があることを確認してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

1. Azure portal で **[ポリシー]** に移動します。
1. サイドバーの **[作成]** セクションで **[定義]** を選択します。
1. "Kubernetes" カテゴリの "GitOps を Kubernetes クラスターにデプロイする" 組み込みポリシーを選択します。 
1. **[割り当て]** をクリックします。
1. **[スコープ]** を、ポリシー割り当てを適用する管理グループ、サブスクリプション、またはリソース グループに設定します。
    * ポリシーのスコープからリソースを除外する場合は、 **[除外]** を設定します。
1. ポリシー割り当てにわかりやすい **[名前]** と **[説明]** を指定します。
1. **[ポリシーの適用]** が "**有効**" に設定されていることを確認します。
1. **[次へ]** を選択します。
1. `sourceControlConfiguration` の作成時に使用するパラメーター値を設定します。
1. **[次へ]** を選択します。
1. **[修復タスクを作成する]** を有効にします。
1. **[マネージド ID を作成します]** がオンになっていること、および ID に **共同作成者** アクセス許可が付与されることを確認します。 
    * 詳細については、[ポリシー割り当ての作成のクイック スタート](../../governance/policy/assign-policy-portal.md)および [Azure Policy に準拠していないリソースの修復に関する記事](../../governance/policy/how-to/remediate-resources.md)を参照してください。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

ポリシー割り当てを作成した後、割り当てのスコープ内にある次のリソースのいずれかに対して `sourceControlConfiguration` が適用されます。
* 新しい `connectedCluster` リソース。
* GitOps エージェントがインストールされている新しい `managedCluster` リソース。 

既存のクラスターに対しては、修復タスクを手動で実行する必要があります。 通常、このタスクには、ポリシー割り当てが有効になるまで 10 ～ 20 分かかります。

## <a name="verify-a-policy-assignment"></a>ポリシーの割り当てを確認する

1. Azure portal で `connectedCluster` リソースのいずれかに移動します。
1. サイドバーの **[設定]** セクションで、 **[ポリシー]** を選択します。 
    * AKS クラスター UX はまだ実装されていません。
    * ポリシー一覧に、前に作成した、 **[コンプライアンスの状態]** が *[準拠している]* に設定されているポリシーの割り当てが表示されます。
1. サイドバーの **[設定]** セクションで、 **[構成]** を選択します。
    * 構成の一覧に、ポリシー割り当てで作成された `sourceControlConfiguration` が表示されます。
1. `kubectl` を使用してクラスターを問い合わせます。 
    * `sourceControlConfiguration` によって作成された名前空間と成果物が表示されます。
    * 5 分以内に、構成された Git リポジトリ内のマニフェストで説明されている成果物がクラスターに表示されます。

## <a name="next-steps"></a>次のステップ

* [Arc 対応 Kubernetes クラスターを使用して Azure Monitor for Containers を設定する](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
