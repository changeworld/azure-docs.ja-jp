---
title: Azure Advisor for PostgreSQL
description: PostgreSQL に関する Azure Advisor の推奨事項について説明します。
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a44f808daf32a1175035005067dfbb5edd664ef6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310055"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor for PostgreSQL
Azure Advisor が Azure Database for PostgreSQL にどのように適用されるかについて説明します。また、よく寄せられる質問に対する回答も示します。
## <a name="what-is-azure-advisor-for-postgresql"></a>Azure Advisor for PostgreSQL とは
Azure Advisor システムでは、テレメトリを使用して、PostgreSQL データベースのパフォーマンスと信頼性に関する推奨事項を発行します。 Advisor による推奨事項は、PostgreSQL データベース オファリングに分割されています。
* Azure Database for PostgreSQL - Single Server
* Azure Database for PostgreSQL - フレキシブル サーバー
* Azure Database for PostgreSQL - Hyperscale (Citus)

いくつかの推奨事項は複数の製品オファリングに共通していますが、それ以外の推奨事項は製品固有の最適化に基づいています。
## <a name="where-can-i-view-my-recommendations"></a>推奨事項が表示される場所
推奨事項は、Azure portal の **[概要]** ナビゲーション サイドバーから確認できます。 プレビューはバナー通知として表示され、リソースの使用状況グラフのすぐ下にある **[通知]** セクションで詳細を表示できます。

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Azure Advisor の推奨事項を示す Azure portal のスクリーンショット。":::

## <a name="recommendation-types"></a>推奨事項の種類
Azure Database for PostgreSQL は、次の種類の推奨事項に優先順位を付けます。
* **パフォーマンス**: PostgreSQL サーバーの処理速度を向上させるため。 これには、CPU 使用率、メモリの不足度、接続プール、ディスク使用率、製品固有のサーバー パラメーターが含まれます。 詳細については、「[Advisor のパフォーマンスに関する推奨事項](../advisor/advisor-performance-recommendations.md)」を参照してください。
* **信頼性**: ビジネスに不可欠なデータベースの継続稼働を保証し、さらに向上させるため。 これには、ストレージの制限、接続の制限、およびハイパースケールのデータ分布に関する推奨事項が含まれます。 詳細については、「[Advisor の信頼性に関する推奨事項](../advisor/advisor-high-availability-recommendations.md)」を参照してください。
* **コスト**: Azure の全体的な支出を最適化し、削減します。 これには、サーバーの適切なサイズ設定に関する推奨事項が含まれます。 詳細については、「[Advisor のコストに関する推奨事項](../advisor/advisor-cost-recommendations.md)」を参照してください。

## <a name="understanding-your-recommendations"></a>推奨事項の内容を理解する
* **日単位のスケジュール**: Azure PostgreSQL データベースの場合、サーバーのテレメトリを確認し、日単位のスケジュールで推奨事項を発行します。 サーバー構成を変更した場合は、翌日テレメトリを再確認するまで、既存の推奨事項は表示されたままになります。 
* **パフォーマンス履歴**: 一部の推奨事項は、パフォーマンス履歴に基づきます。 これらの推奨事項は、サーバーが同じ構成で 7 日間動作している場合にのみ表示されます。 これにより、一定の期間における高い使用率のパターン (高い CPU アクティビティや接続ボリュームなど) を検出できます。 新しいサーバーをプロビジョニングする場合、または新しい仮想コア構成に変更する場合、これらの推奨事項は一時的に停止します。 これにより、以前のテレメトリが、新しく再構成されたサーバーで推奨設定を発行しないようにできます。 ただし、これは、パフォーマンス履歴に基づく推奨事項をすぐに識別できないことも意味します。

## <a name="next-steps"></a>次のステップ
詳細については、[Azure Advisor の概要](../advisor/advisor-overview.md)に関するページを参照してください。
