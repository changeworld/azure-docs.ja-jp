---
title: Application Insights の使用状況と影響 - Azure Monitor
description: さまざまなプロパティがアプリの各部のコンバージョン率に潜在的に及ぼす影響について分析します。
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: 0d09f93c5a0a541929169130feb921a8e65144be
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133236"
---
# <a name="impact-analysis-with-application-insights"></a>Application Insights による影響分析

影響では、読み込み時間とその他のプロパティがアプリのさまざまな部分のコンバージョン率に及ぼす影響について分析します。 より正確に言えば、**ページ ビュー**、**カスタム イベント**、または **要求** の **ディメンション** が、異なる **ページ ビュー** または **カスタム イベント** の使用にどの程度影響するのかについて、明らかにします。 

## <a name="still-not-sure-what-impact-does"></a>影響で行われることがまだわからない場合

影響については、サイトの一部における遅さがユーザーの定着にどの程度影響しているのかについて、チームのメンバーとの議論を収める究極のツールとして考えるのが、1 つの方法です。 ユーザーが一定の遅さを許容する場合がありますが、影響によって、最適化とパフォーマンスのバランスを最善にしてユーザーのコンバージョン率を最大化する方法についての洞察が得られます。

しかし、パフォーマンスの分析は影響の機能の一部にすぎません。 影響ではカスタム イベントとディメンションがサポートされているため、ユーザーのブラウザーの選択と異なるコンバージョン率がどのように相関しているかなどの質問に、わずか数クリックで答えることができます。

> [!NOTE]
> 影響分析ブックを使うには、Application Insights のリソースにページ ビューまたはカスタム イベントが含まれる必要があります。 [アプリをセットアップし、Application Insights JavaScript SDK を使用してページ ビューを自動的に収集する方法について説明します](./javascript.md)。 また、相関関係を分析しているので、サンプルのサイズが問題になることにも注意してください。

## <a name="impact-analytics-workbook"></a>影響分析ブック 

影響分析ブックを使用するには、Application Insights リソースで **[使用状況]**  >  **[影響]** に移動して、 **[Impact Analysis Workbook]\(影響分析ブック\)** を選択します。 または、 **[ブック]** タブで **[パブリック テンプレート]** を選択し、 *[使用状況]* の **[ユーザー影響分析]** を選択します。

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="パブリック テンプレートのブック ギャラリーのスクリーンショット。" lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>ブックを使用する

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="最初のページ ビュー、カスタム イベント、または要求を選択する場所を示すスクリーンショット。" lightbox="./media/usage-impact/selected-event.png":::

1. **[選択されたイベント]** ドロップダウンからイベントを選択します
2. **[次を分析:]** ドロップダウンでメトリックを選択します
3. **[影響するイベント]** ドロップダウンでイベントを選択します 
1. フィルターを追加したい場合は、 **[選択したイベントフィルターの追加]** または **[影響するイベントフィルターの追加]** で行います。


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>ページの読み込み時間がページでのコンバージョン数に影響しているか

影響ブックを使って質問への回答を始めるには、初期ページ ビュー、カスタム イベント、または要求を選択します。

1. **[選択されたイベント]** ドロップダウンからイベントを選択します。
2. **[次を分析]** ドロップダウンを既定の選択である **[期間]** のままにします (このコンテキストでは、 **[期間]** は **ページの読み込み時間** のエイリアスです)。
3. **[影響するイベント]** ドロップダウンで、カスタム イベントを選択します。 このイベントは、手順 1. で選択したページ ビューの UI 要素に対応する必要があります。


:::image type="content" source="./media/usage-impact/impact.png" alt-text="スクリーンショットは、選択したイベントを期間で分析されたホームページとした例を示しています。" lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>カスタムの方法でページ ビューまたは読み込み時間を追跡する場合

影響では、標準とカスタムの両方のプロパティと測定値がサポートされています。 お好きな方をお使いください。 期間の代わりに 1 つ目と 2 つ目のイベントでフィルターを使用すると、より具体的な情報が得られます。

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>ユーザーの国またはリージョンの違いがコンバージョン率の違いに影響するか

1. **[選択されたイベント]** ドロップダウンからイベントを選択します。
2. **[次を分析]** ドロップダウンで [国またはリージョン] を選択します
3. **[影響するイベント]** ドロップダウンでは、手順 1 で選択したページ ビューの UI 要素に対応するカスタム イベントを選択します。

:::image type="content" source="./media/usage-impact/regions.png" alt-text="スクリーンショットは、選択したイベントが国とリージョンによって分析された例を示しています。" lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>影響分析ブックによってこれらのコンバージョン率が計算される方法

内部的には、影響分析ブックは[ピアソン相関関係の係数](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)に依存しています。 結果は -1 と 1 の間で計算されます。ここでは、-1 が負の線形相関を表し、1 が正の線形相関を表します。

影響分析のしくみを単純に分解すると、次のようになります。

_A_ = 最初のドロップダウンで選択するメインのページ ビュー/カスタム イベント/要求とします。 (**選択されたイベント**)。

_B_ = 選択する 2 つ目のページ ビュー/カスタム イベント (**次の使用への影響**) とします。

選択された時間の範囲で、ユーザーからの全セッションのサンプルが影響によって確認されます。 各セッションについて、_A_ のそれぞれの発生が検索されます。

セッションは、2 つの条件のいずれかに基づいて、2 つの異なる種類の "_サブセッション_" に分けられます。

- コンバージョンが行われたサブセッションは、_B_ イベントで終わったセッションで構成され、_B_ の前に発生したすべての _A_ イベントを含みます。
- コンバージョンが行われなかったサブセッションは、_B_ で終わらず、すべての _A_ が発生した場合に発生します。

最終的に影響がどのように計算されるかは、メトリックにより分析しているのか、それともディメンションにより分析しているのかに基づいて異なります。 メトリックでは、サブセッション内のすべての _A_ は平均化されます。 一方ディメンションでは、各 _A_ の値は、_B_ に割り当てられた値への _1/N_ に貢献します。ここで、_N_ はサブセッション内の _A_ の数です。

## <a name="next-steps"></a>次のステップ

- ブックの詳細については、[ブックの概要](../visualize/workbooks-overview.md)に関するページを参照してください。
- 使用状況を把握できるようにするには、[カスタム イベント](./api-custom-events-metrics.md#trackevent)または[ページ ビュー](./api-custom-events-metrics.md#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ファネル](usage-funnels.md)
    - [保持](usage-retention.md)
    - [ユーザー フロー](usage-flows.md)
    - [ブック](../visualize/workbooks-overview.md)
    - [ユーザー コンテキストの追加](./usage-overview.md)