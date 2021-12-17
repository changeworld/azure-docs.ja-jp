---
title: Azure Purview 内のデータに関するアセットの分析情報
description: このハウツー ガイドでは、データに関する Purview Insights のアセット レポートを表示して使用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8879edc7d1858cff5871c5339da4857d81f5c2d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217023"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure Purview 内のデータに関するアセットの分析情報

このハウツー ガイドでは、データに関する Purview のアセットの分析情報レポートへのアクセス、表示、フィルター処理を行う方法について説明します。

> [!IMPORTANT]
> Azure Purview Insights は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このハウツー ガイドでは、次の方法について説明します。

> [!div class="checklist"]
> * Purview アカウントから分析情報を表示します。
> * データの鳥瞰図を取得します。
> * アセット数の詳細をドリル ダウンします。

## <a name="prerequisites"></a>前提条件

Purview Insights の使用を開始する前に、次の手順を完了していることを確認してください。

* Azure リソースを設定し、アカウントにデータを設定します。

* ソースの種類に関するスキャンを設定して完了します。

詳細については、「[Azure Purview でデータ ソースを管理する](manage-data-sources.md)」を参照してください。

## <a name="use-purview-asset-insights"></a>Purview のアセットの分析情報を使用する

Azure Purview では、ソースの種類を登録してスキャンできます。 スキャンが完了したら、[アセットの分析情報] でアセットの分布を表示できます。これは、分類とリソース セットごとのデータ資産の状態を示します。 また、データ サイズの変更があるかどうかも示します。

> [!NOTE]
> ソースの種類をスキャンした後、[アセット分析情報] に新しいアセットが反映されるまで 3 時間から 8 時間ほど待ってください。 展開リージョンのトラフィックの多さ、またはワークロードのサイズが原因で時間がかかる場合があります。 詳細については、フィールド サポート チームにお問い合わせください。

1. Azure portal で Azure Purview リソースに移動します。

1. **[概要]** ページの **[開始]** セクションで、 **[Purview Studio を開く]** タイルを選択します。

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Azure portal から Purview を起動する":::

1. Purview **ホーム** ページで、左側のメニューの **[分析情報]** を選択します。

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Azure portal で分析情報を表示する":::

1. **[分析情報]** 領域で、 **[アセット]** を選択して Purview の **[アセットの分析情報]** レポートを表示します。

### <a name="view-asset-insights"></a>アセットの分析情報を表示する

1. メインの **[アセットの分析情報]** ページには、次の領域が表示されます。

2. ソースの種類、分類されたアセット、検出されたアセットを示す高レベルの KPI
 
3. 最初のグラフは、**ソースの種類ごとのアセット** を示します。

4. ソースの種類ごとのアセットの分布を表示します。 1 つの分類または分類カテゴリ全体を選択すると、ソースの種類ごとのアセットの分布が表示されます。 
 
5. さらに詳細を表示するには、 **[詳細表示]** を選択します。これにより、ソースの種類とアセット数が表形式で表示されます。 このページには分類フィルターも引き継がれます。

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="[アセットの分析情報] で KPI とグラフを表示する":::
 
6. アセット数を含む最上位のフォルダーを表示する特定のソースを選択します。 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="ソースの種類を選択する":::
 
7. 上で選択したソースの種類の中の最上位のフォルダーに対する合計アセット数を選択します。

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="ファイル パスを表示する":::

8. フォルダー内のファイルの一覧を表示します。 階層リンクを使用して元の分析情報に移動します。

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="アセットの一覧を表示する":::  

### <a name="file-based-source-types"></a>ファイル ベースのソースの種類
[アセットの分析情報] の次のいくつかのグラフは、ファイル ベースのソースの種類の分布を示します。 **[ソースの種類の中のファイルの種類のサイズの傾向 (GB)]** という名前の最初のグラフは、過去 30 日間の上位のファイルの種類のサイズの傾向を示します。 
 
1. ソースの種類を選択して、そのソース内のファイルの種類を表示します。 
 
1. **[詳細表示]** を選択して、現在のデータ サイズ、サイズの変更、現在のアセット数、アセット数の変更を表示します。
 
   > [!NOTE]
   > スキャンが過去 30 日間に 1 回しか実行されなかったか、または分類の追加や削除などのカタログの変更が 30 日間に 1 回しか発生していない場合、上の変更情報は空白で表示されます。

1. ソースの種類を選択すると、上位のアセット数の変更を含む最上位のフォルダーが表示されます。

1. パスを選択して、アセットの一覧を表示します。

ファイル ベースのソースの種類の 2 番目のグラフは、***[リソース セットに関連付けられていないファイル]*** です。 すべてのファイルがリソース セットにロールアップされると予測される場合、このグラフは、どのアセットがロールアップされていないかを把握するために役立ちます。 アセットが見つからない場合は、フォルダー内のファイル パターンが間違っていることを示している可能性があります。 ファイルに関する詳細情報を表示するには、他のグラフと同じ手順に従います。

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="ファイル ベースのアセットを表示する":::  

## <a name="next-steps"></a>次のステップ

[スキャンの分析情報](./scan-insights.md)を含む Azure Purview の分析情報レポートの詳細について学習する
