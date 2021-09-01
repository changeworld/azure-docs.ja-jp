---
title: インシデントをさらに分析し、影響を評価する
titleSuffix: Azure Cognitive Services
description: 分析ツールを利用してインシデントをさらに分析する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: 0aefc4207f064550b41f9341e946cae2f1611961
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342404"
---
# <a name="further-analyze-an-incident-and-evaluate-impact"></a>インシデントをさらに分析し、影響を評価する

## <a name="metrics-drill-down-by-dimensions"></a>ディメンション別のメトリックのドリルダウン

インシデント情報を表示する場合には、より詳細な情報を取得することが必要になる場合があります (たとえば、さまざまなディメンションやタイムスタンプについて)。 ご利用のデータに 1 つまたは複数のディメンションが含まれている場合は、ドリルダウン関数を使用して、より詳細な表示を取得できます。 

ドリルダウン関数を使用するには、 **[Incident hub]\(インシデント ハブ\)** の **[Metric drilling]\(メトリックの詳細表示\)** タブをクリックします。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="メトリックの詳細表示":::

**[ディメンション]** 設定は、インシデントに対するディメンションの一覧です。それぞれに対して使用可能な他のディメンション値を選択できます。 ディメンション値が変更された後。 **[タイムスタンプ]** 設定を使用すると、現在のインシデントをさまざまな時点で表示できます。

### <a name="select-drilling-options-and-choose-a-dimension"></a>詳細表示のオプションを選択し、ディメンションを選択します

ドリルダウン オプションには、次の 2 種類があります。**ドリルダウン** と、**水平方向の比較**。

> [!Note]
> - ドリルダウンでは、現在選択されているディメンションを除いて、さまざまなディメンション値からデータを探索できます。 
> - 水平方向の比較では、合計ディメンションを除いて、さまざまなディメンション値からデータを探索できます。

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="ディメンションのドリルダウン":::

### <a name="value-comparison-for-different-dimension-values"></a>さまざまなディメンション値についての値比較

[ドリルダウン] タブの 2 番目のセクションは、さまざまなディメンション値についての比較が含まれるテーブルです。 これには、値、初期値、差分値、デルタ値、および異常の有無が含まれます。
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="ドリルダウンの比較" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>さまざまなディメンション値についての値および予期される値の比較

[ドリルダウン] タブの 3 番目のセクションは、さまざまなディメンション値に関するヒストグラムであり、値および予期される値が含まれます。 このヒストグラムは、値と予期される値との差に基づいて並べ替えられます。 最も大きな影響のある予期しない値を容易に見つけることができます。 たとえば、上の図では、合計値を除いて、**US7** が異常の最も大きな原因になっているいることがわかります。

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="ドリルダウン テーブル" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>未処理の値の視覚化
[ドリルダウン] タブの最後の部分は、未処理の値の折れ線グラフです。 このグラフを使用すると、メトリック ページに移動して詳細を表示する必要がなくなります。

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="ドリルダウン折れ線グラフ" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="compare-time-series"></a>時系列の比較

特定の時系列で異常が検出されたとき、それを、1 つの視覚化で他の複数の系列と比較すると役立つ場合があります。 **[比較ツール]** タブをクリックしてから、青色の **[+ 追加]** ボタンをクリックします。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="比較する系列の追加" lightbox="../media/diagnostics/add-series.png":::

データ フィードから系列を選択します。 同じ細分性を選択することも別の細分性を選択することもできます。 ターゲット ディメンションを選択し、系列の傾向を読み込んでから、 **[OK]** をクリックして、それを前の系列と比較します。 系列は 1 つの視覚化にまとめられます。 引き続き比較対象の系列を追加して、詳細な分析情報を得ることができます。 **[比較ツール]** タブの上部にあるドロップダウン メニューをクリックして、時間がシフトされた期間にわたって時系列データを比較します。  

> [!Warning]
> 比較を行うには、時系列データ分析でデータ ポイントのシフトが必要になる場合があるため、データの細分性によるサポートが必要です。 たとえば、データが週単位である場合、 **[Day over day]\(前日比\)** 比較を使用した場合、結果は得られません。 この例では、 **[Month over month]\(前月比\)** 比較を代わりに使用します。

時間シフトの比較を選択したら、データ値、デルタ値、またはパーセント デルタのどれを比較対象とするのかを選択できます。

## <a name="view-similar-anomalies-using-time-series-clustering"></a>時系列クラスタリングを使用して類似した異常を表示する

インシデントを表示する場合は、それに関連するさまざまな系列を、 **[Similar time-series-clustering]\(類似の時系列クラスタリング\)** タブを使用して表示できます。 1 つのグループ内の系列は一緒にまとめられます。 上の図から、少なくとも 2 つの系列グループがあることがわかります。 この機能を使用できるのは、次の要件が満たされている場合に限られます。

- メトリックには 1 つまたは複数のディメンションまたはディメンション値が必要です。
- 1 つのメトリック内の系列の傾向は類似している必要があります。

使用可能なディメンションは、このタブの上部に一覧表示され、系列を指定するための選択を行うことができます。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="系列グループ":::