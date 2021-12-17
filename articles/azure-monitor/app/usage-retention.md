---
title: Application Insights による Web アプリのユーザー リテンション期間分析
description: アプリに戻るユーザーの数を分析します。
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: e99467f43f3a5ce1e9dd300918414a9968bd2045
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131906"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Azure Application Insights による Web アプリケーションのユーザー リテンション期間分析

[Application Insights](./app-insights-overview.md) のリテンション期間機能を使用すると、アプリに戻るユーザーの数、およびそのユーザーが特定のタスクを実行したり目標を達成したりする頻度を分析できます。 たとえば、ゲームのサイトを運営する場合は、ゲームに負けた後にサイトに戻るユーザーの数と、勝った後に戻るユーザーの数を比較できます。 このデータによって、ユーザー エクスペリエンスとビジネス戦略の両方の強化に役立ちます。

## <a name="get-started"></a>はじめに

Application Insights ポータルのリテンション期間ツールにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](usage-overview.md)してください。

## <a name="the-retention-workbook"></a>リテンション期間ブック

リテンション期間ブックを使用するには、Application Insights リソースで **[使用状況]** 、 **[リテンション期間]** の順に移動し、 **[Retention Analysis Workbook]\(リテンション期間分析ブック\)** を選択します。 または、 **[ブック]** タブで **[パブリック テンプレート]** を選択し、 *[使用状況]* の **[User Retention Analysis]\(ユーザー リテンション期間分析\)** を選択します。

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="パブリック テンプレートのブック ギャラリーのスクリーンショット。" lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>ブックを使用する

:::image type="content" source="./media/usage-retention/retention.png" alt-text="折れ線グラフを示すリテンション期間ブックのスクリーンショット。" lightbox="./media/usage-retention/retention.png":::

- 既定では、特定の期間に何かを実行して再び戻ってきた後、さらに何か他のことを実行したすべてのユーザーが表示されます。 各種のイベントを組み合わせることで、特定のユーザーのアクティビティを絞り込むことができます。
- **[フィルターの追加]** を選択し、1 つまたは複数のフィルターをプロパティに追加します。 たとえば、特定の国またはリージョンのユーザーに注目することができます。 
- "保有期間全体" のグラフには、選択された期間におけるユーザーのリテンションのサマリーが表示されます。 
- グリッドには、保有されたユーザー数が表示されます。 各行は、表示された期間に選択したいずれかのイベントを実行したユーザーのコーホートを表します。 行の各セルは、そのコーホートの中で、その後の期間に 1 回以上戻ったユーザーの数を示しています。 複数の期間にユーザーが戻る可能性があります。 
- リテンション期間レポートを把握しやすいよう、開始イベントの上位 5 件と返されたイベントの上位 5 件が洞察カードに表示されます。 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="リテンション期間ブックのスクリーンショット。# 週後に戻るユーザーのグラフを確認できます。" lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>ビジネス イベントを使用してリテンション期間を追跡する

最も役立つリテンション期間分析を行うには、重要なビジネス アクティビティを表すイベントを測定します。 

たとえば、多くのユーザーは、アプリでページを開いても、そこに表示されるゲームをプレイしない可能性があります。 そのため、ページ ビューだけを追跡すると、前回ゲームをプレイし、再度プレイするために戻ったユーザーの正確な推定数を得ることができません。 リピート プレイヤーを明確に把握するには、ユーザーが実際にプレイするときに、アプリからカスタム イベントを送信する必要があります。  

重要なビジネス アクションを表すカスタム イベントをコーディングして、それらをリテンション期間分析に使用することをお勧めします。 ゲームの結果をキャプチャするには、カスタム イベントを Application Insights に送信するためのコード行を記述する必要があります。 Web ページ コードまたは Node.JS で記述した場合のコード行は次のようになります。

```JavaScript
    appinsights.trackEvent("won game");
```

また、ASP.NET サーバー コードでは、次のようになります。

```csharp
   telemetry.TrackEvent("won game");
```

カスタム イベントの記述について詳しくは、[こちら](./api-custom-events-metrics.md#trackevent)をご覧ください。


## <a name="next-steps"></a>次のステップ
- - ブックの詳細については、[ブックの概要](../visualize/workbooks-overview.md)に関するページを参照してください。
- 使用状況を把握できるようにするには、[カスタム イベント](./api-custom-events-metrics.md#trackevent)または[ページ ビュー](./api-custom-events-metrics.md#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ユーザー、セッション、イベント](usage-segmentation.md)
    - [ファネル](usage-funnels.md)
    - [ユーザー フロー](usage-flows.md)
    - [ブック](../visualize/workbooks-overview.md)
    - [ユーザー コンテキストの追加](./usage-overview.md)