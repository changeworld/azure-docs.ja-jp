---
title: 新機能
description: Azure Synapse Analytics の新機能とドキュメントの改善について説明します
services: synapse-analytics
author: ryanmajidi
ms.author: rymajidi
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: e86d15e5b15133891ad5fd3e3f61ea6cc6f790e5
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503510"
---
# <a name="whats-new-in-azure-synapse-analytics"></a>Azure Synapse Analytics の新機能

この記事では、2021 年 10 月時点で利用可能な Azure Synapse Analytics の更新の一覧を示します。

各更新は、Azure Synapse Analytics ブログと詳細情報が記載された記事にリンクされています。

## <a name="october-2021-update"></a>2021 年 10 月の更新

次の更新は、今月 Azure Synapse Analytics に追加されたものです。

### <a name="general"></a>全般

- Azure Synapse の購入前プランを使用したコスト管理に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#manage-cost)と[記事](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md)
- Azure リージョン間での Azure Synapse ワークスペースの移動に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#move-workspace-region)と[記事](how-to-move-workspace-from-one-region-to-another.md)

### <a name="apache-spark-for-synapse"></a>Synapse 用の Apache Spark

- Spark のパフォーマンスの最適化に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#spark-performance) 

### <a name="security"></a>セキュリティ

- すべての Synapse RBAC ロールが運用環境向けに一般提供されたことに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-rbac)と[記事](./security/synapse-workspace-synapse-rbac-roles.md)
- ユーザー割り当てマネージド ID を活用した二重暗号化に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#user-assigned-managed-identities)と[記事](./security/workspaces-encryption.md)
- Synapse 管理者に専用 SQL プールへの昇格されたアクセス権が付与されたことに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#elevated-access)と[記事](./security/synapse-workspace-access-control-overview.md)
    
###  <a name="governance"></a>ガバナンス

- Synapse ワークスペースから Azure Purview に系列データを自動的にプッシュできるようになったことに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-purview-lineage)と[記事](../purview/how-to-lineage-azure-synapse-analytics.md)
 
### <a name="integrate"></a>統合

- データ フローで文字列化を使用して複雑なデータ型を文字列に簡単に変換する方法に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#stringify-transform)と[記事](../data-factory/data-flow-stringify.md)
- データ フローでの Spark セッションの有効期限 (TTL) の制御に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#data-flowspark-ttl)と[記事](../data-factory/concepts-integration-runtime-performance.md)

### <a name="cicd--git"></a>CI/CD および Git

- GitHub Actions を使用した Synapse ワークスペースのデプロイに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#deploy-synapse-github-action)と[記事](./cicd/continuous-integration-delivery.md#configure-github-actions-secrets)
- Synapse Studio での Git ブランチ作成の制御強化に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#create-git-branch-in-studio)と[記事](./cicd/source-control.md#creating-feature-branches)

### <a name="developer-experience"></a>開発者エクスペリエンス

- Synapse ノートブック プレビューでの Markdown の編集機能の強化に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-markdown-toolbar)と[記事](./spark/apache-spark-development-using-notebooks.md)
- Pandas DataFrames による、適切に書式設定された HTML テーブルの自動レンダリングに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#pandas-dataframe-html)と[記事](./spark/apache-spark-data-visualization.md)
- Synapse ノートブックでの IPython ウィジェットの使用に関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-ipythong-widgets)と[記事](./spark/apache-spark-development-using-notebooks.md)
- Python および Scala で Mssparkutils ランタイム コンテキストが利用可能になったことに関する[ブログ](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#mssparkutils-context)と[記事](./spark/microsoft-spark-utilities.md?pivots=programming-language-python)

## <a name="next-steps"></a>次の手順

[Azure Synapse Analytics の使用を開始する](get-started.md)


