---
title: コンピューティング最適化の廃止
description: データ フロー コンピューティング最適化オプションは廃止される予定です
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: tutorial
ms.date: 06/29/2021
ms.openlocfilehash: c0cfe16f92c3fb3270b3c5f9398c3795c9c83931
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720115"
---
# <a name="retirement-of-data-flow-compute-optimized-option"></a>データ フロー コンピューティング最適化オプションの廃止

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory と Azure Synapse Analytics のデータ フローでは、グラフィカル デザイン パラダイムを使用して ETL ジョブのデータを大規模に変換する、ローコードのメカニズムが提供されています。 データ フローは、Azure Data Factory と Azure Synapse Analytics のサーバーレス Integration Runtime 機能で実行されます。 Azure Data Factory と Azure Synapse Analytics の Integration Runtime のスケーラブルな性質により、データ フローの大規模な実行に使用される Azure Databricks Spark 環境に対して、メモリ最適化、General Purpose、コンピューティング最適化の 3 つの異なるコンピューティング オプションが使用可能でした。 運用ワークロード向けに Integration Runtime で使用するデータ フロー コンピューティングの推奨されるクラスは、メモリ最適化と General Purpose です。 コンピューティング最適化は多くの場合、データ フローの一般的な使用例にとって十分でないため、運用ワークロードでは General Purpose またはメモリ最適化のデータ フローを使用することをお勧めします。

## <a name="migration-steps"></a>移行の手順

現在から 2024 年 8 月 31 日までは、コンピューティング最適化データ フローは既存のパイプラインで引き続き機能します。 サービスの中断を回避するために、2024 年 8 月 31 日より前に既存のコンピューティング最適化データ フローを削除し、下の手順に従って、新しい Azure Integration Runtime とデータ フロー アクティビティを作成してください。 新しいデータ フロー アクティビティを作成するには、次のようにします。

1. コンピューティングの種類として "General Purpose" または "メモリ最適化" を使用して、新しい Azure Integration Runtime を作成します。
2. これらのいずれかのコンピューティングの種類を使用して、データ フロー アクティビティを設定します。

   :::image type="content" source="media/data-flow/compute-types.png" alt-text="コンピューティングの種類":::

## <a name="comparison-between-different-compute-options"></a>異なるコンピューティング オプションの比較 

| コンピューティング オプション              | パフォーマンス                                                  |
| :-------------------- | :----------------------------------------------------------- |
| General Purpose データ フロー (Basic) | 運用ワークロードでの一般的な使用例に適しています |
| メモリ最適化データ フロー (Standard) | 大規模なデータセットと多くの計算を処理する場合のデータ フローに合ったパフォーマンスの最も高いランタイム |
| コンピューティング最適化データ フロー (非推奨) | 運用環境のワークロードにはお勧めしません |

* [Azure Data Factory の価格ページで、General Purpose とメモリ最適化のデータ フローに対して用意された最新の更新済み価格を参照する](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
* [こちらのデータ フローの FAQ で詳細情報を見つける](./frequently-asked-questions.yml#mapping-data-flows)  
* [Microsoft Q&A でデータ フローに関する質問を投稿し、回答を見つける](/answers/questions/topics/azure-data-factory.html)