---
title: Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy を使用してクラスター構成を大規模に適用する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779960"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)

## <a name="overview"></a>概要

各 `Microsoft.Kubernetes/connectedclusters` リソース、または GitOps 対応の `Microsoft.ContainerService/managedClusters` リソースに特定の `Microsoft.KubernetesConfiguration/sourceControlConfigurations` が適用されるように強制するには、Azure Policy を使用します。  Azure Policy を使用するには、既存のポリシー定義を選択して、ポリシーの割り当てを作成します。  ポリシーの割り当てを作成する場合、割り当てのスコープを設定します。これは、Azure リソース グループまたはサブスクリプションになります。  また、作成する `sourceControlConfiguration` のパラメーターも設定します。  割り当てを作成すると、スコープ内のすべての `connectedCluster` または `managedCluster` リソースをポリシー エンジンが識別し、それぞれに対して `sourceControlConfiguration` を適用します。

各クラスターの信頼できるソースとして複数の Git リポジトリを使用している場合 (たとえば、1 つのリポジトリは中央 IT/クラスター オペレーター用、別のリポジトリはアプリケーション チーム用として使用している場合)、異なる Git リポジトリを使用するように各ポリシー割り当てを構成して、複数のポリシー割り当てを使用することにより、これを有効にすることができます。

## <a name="create-a-custom-policy-definition"></a>カスタム ポリシー定義の作成

1. Azure portal で、[ポリシー] に移動し、サイドバーの **[作成]** セクションで、 **[定義]** を選択します。
2. **[+ ポリシーの定義]** を選択します。
3. **[定義の場所]** をご使用のサブスクリプションまたは管理グループに設定します。  これにより、ポリシーの定義を使用できる最も広範なスコープが決まります。
4. ポリシーの **[名前]** と **[説明]** を設定します。
5. [カテゴリ] で、 **[新規作成]** を選択し、"*Kubernetes クラスター - Azure Arc*" を作成します。
6. **[ポリシー ルール]** 編集ボックスに、この[ポリシーの定義の例](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json)をコピー/貼り付けします。
7. **保存**します。

カスタムのポリシーの定義を作成するこの手順は、作業が完了してこれが組み込みポリシーになった後は必要ありません。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

1. Azure portal で、[ポリシー] に移動し、サイドバーの **[作成]** セクションで、 **[定義]** を選択します。
2. 作成したばかりの定義を見つけて、選択します。
3. アクションのページで、 **[割り当て]** を選択します。
4. **[スコープ]** を、ポリシーの割り当てを適用する管理グループ、サブスクリプション、またはリソース グループに設定します。
5. ポリシーのスコープからリソースを除外する場合は、 **[除外]** を設定します。
6. ポリシーの割り当てに、簡単に識別するために使用できる **[名前]** と **[説明]** を設定します。
7. **[ポリシーの適用]** が、"*有効*" に設定されていることを確認します。
8. **[次へ]** を選択します。
9. `sourceControlConfiguration` の作成時に使用されるパラメーター値を設定します。
10. **[次へ]** を選択します。
11. **[修復タスクを作成する]** を有効にします。
12. **[マネージド ID を作成します]** がオンになっていること、および ID に **共同作成者**アクセス許可が付与されることを確認します。  必要なアクセス許可の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)および[こちらのドキュメントのコメント](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)を参照してください。
13. **[Review + create]\(レビュー + 作成\)** を選択します。

ポリシーの割り当てを作成した後、割り当てのスコープ内にあるすべての新しい `connectedCluster` リソース (または GitOps エージェントがインストールされている `managedCluster` リソース) に対して、`sourceControlConfiguration` が適用されます。  既存のクラスターに対しては、修復タスクを手動で実行する必要があります。  通常、ポリシーの割り当てが有効になるまで 10 から 20 分かかります。

## <a name="verify-a-policy-assignment"></a>ポリシーの割り当てを確認する

1. Azure portal で、`connectedCluster` リソースのいずれかに移動し、サイドバーの **[設定]** セクションで、 **[ポリシー]** を選択します (AKS マネージド クラスターの UX はまだ実装されていませんが、実装される予定です)。
2. 一覧に、上記で作成したポリシーの割り当てが表示され、 **[コンプライアンスの状態]** は "*準拠している*" になっています。
3. サイドバーの **[設定]** セクションで、 **[構成]** を選択します。
4. 一覧に、ポリシーの割り当てで作成された `sourceControlConfiguration` が表示されます。
5. **kubectl** を使用して、クラスターに問い合わせます。`sourceControlConfiguration` によって作成された名前空間と成果物が表示されます。
6. 5 分以内に、構成された Git リポジトリ内のマニフェストで説明されている成果物がクラスターに表示されます。

## <a name="next-steps"></a>次のステップ

* [Arc 対応 Kubernetes クラスターを使用して Azure Monitor for Containers を設定する](./deploy-azure-monitor-for-containers.md)
