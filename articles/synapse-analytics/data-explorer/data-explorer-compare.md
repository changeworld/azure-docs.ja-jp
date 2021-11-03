---
title: Azure Data Explorer と Azure Synapse Data Explorer の違い (プレビュー)
description: この記事では、Azure Data Explorer と Azure Synapse Data Explorer の違いについて説明します。
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: a6368d0431d2ce388f2d5bed37d125a34b4d5094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029606"
---
# <a name="what-is-the-difference-between-azure-synapse-data-explorer-and-azure-data-explorer-preview"></a>Azure Synapse Data Explorer と Azure Data Explorer の違いは何ですか? (プレビュー)

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) は、ログと利用統計情報のための高速で拡張性に優れた、スタンドアロンのデータ探索サービスです。 このサービスの実行の基となるテクノロジと同じものが、Azure Synapse で統合分析サービスとして利用できます。これは、データ ウェアハウスとデータ エンジニアリングの機械学習シナリオ向けに最適化された既存の SQL および Spark サービスを補完します。

Data Explorer 機能のネイティブな統合により、分析ログと時系列ワークロードが Synapse Data Explorer プールの形式で Synapse で利用可能になります。 これらのプールにより、他の Synapse 機能との密接な統合が実現し、これらの機能への接続管理が容易になります。 すべてのマシンとユーザー データの利用とアクセスが 1 か所でできるようになり、Data Explorer のほぼリアルタイムの探索的機能を活用することで、ビジネス価値を高めるソリューションを構築できます。

ビッグ データと分析機能を組み合わせた統合ソリューションをお求めの場合は、Synapse Data Explorer で始めることをお勧めします。

## <a name="capability-support"></a>機能のサポート

| カテゴリ | 機能 | Azure Data Explorer | Synapse Data Explorer |
|--|--|--|--|
| **Security** | VNet | VNet インジェクションと Azure Private Link のサポート | Synapse マネージド VNet の一部として自動的に統合される Azure Private Link のサポート |
|  | CMK | ✓ | Synapse ワークスペースの構成から自動的に継承 |
|  | ファイアウォール | ✗ | Synapse ワークスペースの構成から自動的に継承 |
| **ビジネス継続性** | 可用性ゾーン | 省略可能 | Availability Zones が使用可能な場合は既定で有効 |
| **SKU** | コンピューティング オプション | 22 以上の Azure VM SKU から選択 | Synapse ワークロードの種類の SKU に簡略化 |
| **統合** | 組み込みのインジェスト パイプライン | Event Hub、Event Grid、IoT Hub | 非マネージド VNet の Azure portal を介した Event Hub、Event Grid、および IoT Hub |
|  | Spark の統合 | Azure Data Explorer のリンク サービス: Azure Active Directory パススルー認証、Synapse ワークスペース MSI、サービス プリンシパルをサポートする組み込みの Kusto Spark 統合 | Azure Active Directory パススルー認証、Synapse ワークスペース MSI、サービス プリンシパルをサポートする組み込みの Kusto Spark コネクタ統合 |
|  | KQL 成果物の管理 | ✗ | KQL クエリの保存と Git との統合 |
|  | メタデータの同期 | ✗ | 他の Synapse サービスによる消費のための中央の Synapse メタ ストアとの外部テーブルの同期 |
| **機能** | KQL のクエリ | ✓ | ✓ |
|  | API と SDK | ✓ | ✓ |
|  | Connectors | ✓ | ✓ |
|  | クエリ ツール | ✓ | ✓ |
| **料金** | ビジネス モデル | 複数のメーターを含むコストと課金モデル: Azure Data Explorer IP マークアップ、Compute、Storage、ネットワーク | 2 つのメーターを持つ仮想コア課金モデル: 仮想コアと Storage |
|  | 予約インスタンス | ✓ | ✗ |

## <a name="next-steps"></a>次のステップ

[Azure Synapse Analytics の Data Explorer](data-explorer-overview.md)
