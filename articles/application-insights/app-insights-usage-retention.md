---
title: "Azure Application Insights による Web アプリケーションのユーザー リテンション期間分析 | Microsoft Docs"
description: "アプリに戻るユーザーの数を分析します。"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Azure Application Insights による Web アプリケーションのユーザー リテンション期間分析

[Azure Application Insights](app-insights-overview.md) のリテンション期間ブレードを使用すると、アプリに戻るユーザーの数、およびそのユーザーが特定のタスクを実行したり目標を達成したりする頻度を分析できます。 たとえば、ゲームのサイトを運営する場合は、ゲームに負けた後にサイトに戻るユーザーの数と、勝った後に戻るユーザーの数を比較できます。 このデータによって、ユーザー エクスペリエンスとビジネス戦略の両方の強化に役立ちます。

## <a name="get-started"></a>作業開始

Application Insights ポータルのリテンション期間ブレードにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](app-insights-usage-overview.md)してください。

## <a name="the-retention-tool"></a>リテンション期間ツール

![リテンション期間ツール](./media/app-insights-usage-retention/retention.png)

a. [概要] ページは常に表示されます。 また、別のパラメーターの設定を使用して名前付きのレポートを保存できます。 リソースへのアクセス権を持つ他のユーザーにレポートを表示するには、レポートを共有に保存します。

b. 既定では、アプリから受信したいずれかのカスタム イベントまたはページ ビューを使用したすべてのユーザーがグラフによってカウントされます。 特定のユーザー アクティビティに注目するには、1 つ (サブセット) を選択します。 

c. プロパティに対して 1 つ以上のフィルターを選択します。 たとえば、特定の国またはリージョンのユーザーに注目することができます。 フィルターの設定が完了したら、**[更新]** をクリックします。

d. **[既定値に戻す]** を使用すると、カスタム フィルターとイベント フィルターが常にクリアされます。

e. 概要グラフには、選択された期間にわたる合計値が表示されます。

f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 このグリッドには、特定の期間内に選択したアクションを繰り返し実行するために戻ったユーザーの数が表示されます。 各行は、表示された期間に選択したアクションのいずれかを実行したユーザーのコーホートを表します。 行の各セルは、そのコーホートの中で、その後の期間に 1 回以上戻ったユーザーの数を示しています。 複数の期間にユーザーが戻る可能性があります。

グラフに示す時間の範囲内にアプリを使用したすべてのユーザーがグラフの 1 行として表されます。 各ユーザーは、グラフに示す時間の範囲内に選択したアクションを最初に実行した期間にカウントされます。 そのため、最初の行の数が大きくなる傾向があります。


## <a name="use-business-events-to-track-retention"></a>ビジネス イベントを使用してリテンション期間を追跡する

最も役立つリテンション期間分析を行うには、重要なビジネス アクティビティを表すイベントを測定します。 

たとえば、多くのユーザーは、アプリでページを開いても、そこに表示されるゲームをプレイしない可能性があります。 そのため、ページ ビューだけを追跡すると、前回ゲームをプレイし、再度プレイするために戻ったユーザーの正確な推定数を得ることができません。 リピート プレイヤーを明確に把握するには、ユーザーが実際にプレイするときに、アプリからカスタム イベントを送信する必要があります。  

重要なビジネス アクションを表すカスタム イベントをコーディングして、それらをリテンション期間分析に使用することをお勧めします。 ゲームの結果をキャプチャするには、カスタム イベントを Application Insights に送信するためのコード行を記述する必要があります。 Web ページ コードまたは Node.JS で記述した場合のコード行は次のようになります。

```JavaScript
    appinsights.trackEvent("won game");
```

また、ASP.NET サーバー コードでは、次のようになります。

```C#
   telemetry.TrackEvent("won game");
```

カスタム イベントの記述について詳しくは、[こちら](app-insights-api-custom-events-metrics.md#trackevent)をご覧ください。


## <a name="next-steps"></a>次のステップ

* [利用状況の概要](app-insights-usage-overview.md)
* [ユーザーとセッション](app-insights-usage-segmentation.md)
* [カスタム イベントのコーディング](app-insights-api-custom-events-metrics.md)


