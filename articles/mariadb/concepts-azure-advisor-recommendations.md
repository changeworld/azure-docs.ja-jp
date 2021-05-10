---
title: Azure Advisor for MariaDB
description: MariaDB に関する Azure Advisor の推奨事項について説明します。
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368484"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor for MariaDB
Azure Advisor が Azure Database for MariaDB にどのように適用されるかを説明します。また、よく寄せられる質問に対する回答も示します。
## <a name="what-is-azure-advisor-for-mariadb"></a>Azure Advisor for MariaDB とは
Azure Advisor システムでは、テレメトリを使用して、MariaDB データベースのパフォーマンスと信頼性に関する推奨事項を発行します。 

いくつかの推奨事項は複数の製品オファリングに共通していますが、それ以外の推奨事項は製品固有の最適化に基づいています。
## <a name="where-can-i-view-my-recommendations"></a>推奨事項が表示される場所
推奨事項は、Azure portal の **[概要]** ナビゲーション サイドバーから確認できます。 プレビューはバナー通知として表示され、リソースの使用状況グラフのすぐ下にある **[通知]** セクションで詳細を表示できます。

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Azure Advisor の推奨事項を示す Azure portal のスクリーンショット。":::

## <a name="recommendation-types"></a>推奨事項の種類
Azure Database for MariaDB は、次の種類の推奨事項に優先順位を付けます。
* **パフォーマンス**: 目的は、MariaDB サーバーの処理速度を向上させることです。 これには、CPU 使用率、メモリの不足度、ディスク使用率、製品固有のサーバー パラメーターが含まれます。 詳細については、「[Advisor のパフォーマンスに関する推奨事項](../advisor/advisor-performance-recommendations.md)」を参照してください。
* **信頼性**: 目的は、ビジネスに不可欠なデータベースの継続稼働を保証し、さらに向上させることです。 これには、ストレージの上限値や、接続数の制限に関する推奨事項が含まれます。 詳細については、「[Advisor の信頼性に関する推奨事項](../advisor/advisor-high-availability-recommendations.md)」を参照してください。
* **コスト**: Azure の全体的な支出を最適化し、削減します。 これには、サーバーの適切なサイズ設定に関する推奨事項が含まれます。 詳細については、「[Advisor のコストに関する推奨事項](../advisor/advisor-cost-recommendations.md)」を参照してください。

## <a name="understanding-your-recommendations"></a>推奨事項の内容を理解する
* **日単位のスケジュール**: Azure MariaDB データベースの場合、サーバーのテレメトリを確認し、日単位のスケジュールで推奨事項を発行します。 サーバー構成を変更した場合は、翌日テレメトリを再確認するまで、既存の推奨事項は表示されたままになります。 
* **パフォーマンス履歴**: 一部の推奨事項は、パフォーマンス履歴に基づきます。 これらの推奨事項は、サーバーが同じ構成で 7 日間動作している場合にのみ表示されます。 これにより、一定の期間における高い使用率のパターン (高い CPU アクティビティや接続ボリュームなど) を検出できます。 新しいサーバーをプロビジョニングする場合、または新しい仮想コア構成に変更する場合、これらの推奨事項は一時的に停止します。 これにより、以前のテレメトリが、新しく再構成されたサーバーで推奨設定を発行しないようにできます。 ただし、これは、パフォーマンス履歴に基づく推奨事項をすぐに識別できないことも意味します。

## <a name="next-steps"></a>次のステップ
詳細については、[Azure Advisor の概要](../advisor/advisor-overview.md)に関するページを参照してください。
