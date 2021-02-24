---
title: Azure Purview 内のデータに関するスキャンの分析情報
description: このハウツー ガイドでは、データに関する Purview Insights のスキャン レポートを表示して使用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575790"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure Purview 内のデータに関するスキャンの分析情報

このハウツー ガイドでは、データに関する Azure Purview のスキャンの分析情報レポートへのアクセス、表示、フィルター処理を行う方法について説明します。

このハウツー ガイドでは、次の方法について説明します。

> [!div class="checklist"]
> * Purview アカウントから分析情報を表示します。
> * スキャンの鳥瞰図を取得します。

## <a name="prerequisites"></a>前提条件

Purview Insights の使用を開始する前に、次の手順を完了していることを確認してください。

* Azure リソースを設定し、アカウントにデータを設定します。
* データ ソースに関するスキャンを設定して完了します。

詳細については、[Azure Purview でのデータ ソースの管理 (プレビュー)](manage-data-sources.md) に関するページを参照してください。

## <a name="use-purview-scan-insights"></a>Purview のスキャンの分析情報を使用する

Azure Purview では、ソースの種類を登録してスキャンできます。 スキャンの状態は、時間の経過と共にスキャンの分析情報に表示できます。 この分析情報は、特定の期間内に何回のスキャンが失敗したか、成功したか、または取り消されたかを示します。

### <a name="view-scan-insights"></a>スキャンの分析情報を表示する

1. Azure portal で **Azure Purview** のインスタンス画面に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[開始]** セクションで、 **[Purview Studio を開く]** タイルを選択します。

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure portal から Purview を起動する":::

1. Purview の **[ホーム]** ページで、 **[分析情報の表示]** タイルを選択して **[分析情報]** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 領域にアクセスします。

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure portal で分析情報を表示する":::

1. **[分析情報]** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 領域で、 **[スキャン]** を選択して Purview の **[スキャンの分析情報]** レポートを表示します。

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>高レベルの KPI を表示して状態ごとのスキャンの回数を示す
 
高レベルの KPI は、ある期間内に実行された合計のスキャン回数を示します。 この期間の既定値は過去 30 日間です。 ただし、過去 7 日間も選択できます。 時間フィルターに基づいて、KPI 値にはスキャンの回数が適切に反映されます。


選択した時間フィルターの値に基づいて、週または日ごとの成功したスキャン、失敗したスキャン、取り消されたスキャンの分布をグラフで確認できます。

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="スキャンの分析情報を表示する":::

## <a name="next-steps"></a>次のステップ

[アセットの分析情報](./asset-insights.md)を含む Azure Purview の分析情報レポートの詳細について学習する
