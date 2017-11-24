---
title: "パラメーター化 URL を使用して Azure Time Series Insights のカスタム ビューを共有する | Microsoft Docs"
description: "この記事では、パラメーター化した URL を Azure Time Series Insights で構築し、カスタマー ビューを簡単に共有できるようにする方法について説明します。"
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: ac48969a9166080384dccf606f0401a82016a60a
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>パラメーター化 URL を使用してカスタム ビューを共有する

Time Series Insights エクスプローラーのカスタム ビューを共有するために、プログラムからカスタム ビューのパラメーター化 URL を作成することができます。

Time Series Insights エクスプローラーでは、そのエクスペリエンスのビューを URL から直接指定する URL クエリ パラメーターがサポートされています。  たとえば、ターゲット環境や検索述語、必要な期間を URL だけで指定することができます。 そのカスタマイズした URL をユーザーがクリックすると、Time Series Insights ポータル内の対応するアセットにインターフェイスから直接アクセスすることができます。  データ アクセス ポリシーが適用されます。 

## <a name="environment-id"></a>環境 ID

`environmentId=<guid>` パラメーターは、ターゲット環境 ID を指定します。  これはデータ アクセス FQDN の構成要素であり、Azure Portal では、環境の概要の右上隅に表示されます。  `env.timeseries.azure.com` の前に存在するすべての文字列が該当します。 たとえば、環境 ID パラメーターは `?environmentId=10000000-0000-0000-0000-100000000108` のようになっています

## <a name="time"></a>Time

パラメーター化 URL では、絶対時刻または相対時刻の値を指定できます。

### <a name="absolute-time-values"></a>絶対時刻値

絶対時刻値には、`from=<integer>` パラメーターと `to=<integer>` パラメーターを使用します。 

`from=<integer>` には、検索範囲の開始時刻を JavaScript のミリ秒表現で指定します。

`to=<integer>` には、検索範囲の終了時刻を JavaScript のミリ秒表現で指定します。 

JavaScript における日付のミリ秒表現については、「[Epoch & Unix Timestamp Converter (Epoch & Unix タイムスタンプ コンバーター)](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)」を参照してください。

### <a name="relative-time-values"></a>相対時刻値

相対時刻値には、`relativeMillis=<value>` を使用します。*value* は、終了時刻の直近データを基準とする時間を JavaScript のミリ秒表現で指定します。

たとえば「`&relativeMillis=3600000`」を指定した場合、直近 60 分のデータが表示されます。

指定できる値は、Time Series Insights エクスプローラーの **[quick time]\(クイック タイム\)** メニューに対応し、次の値が該当します。

- 1800000 (直近 30 分)
- 3600000 (直近 60 分)
- 10800000 (直近 3 時間)
- 21600000 (直近 6 時間)
- 43200000 (直近 12 時間)
- 86400000 (直近 24 時間)
- 604800000 (直近 7 日)
- 2592000000 (直近 30 日)

### <a name="optional-parameters"></a>省略可能なパラメーター

`timeSeriesDefinitions=<collection of term objects>` パラメーターは、Time Series Insights ビューの期間を指定します。

- `name=<string>`
  - "*期間*" の名前。
- `splitBy=<string>`
  - "*分割基準*" となる列名。
- `measureName=<string>`
  - "*メジャー*" の列名。
- `predicate=<string>`
  - サーバー側フィルター処理の *where* 句。

 
### <a name="examples"></a>例

たとえば URL パラメーターとして時系列の定義を追加するには、以下を使用します。

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

この例の時系列定義を以下に対して使用するとします。 

- 環境 ID
- 直近 60 分のデータ
- オプション パラメーターを構成する期間 (F1PressureID、F2TempStation、F3VibrationPL)
 
この場合、ビューのパラメーター化 URL は、次のように構築することができます。

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

前述の URL によって表されるビューを Time Series Insights エクスプローラーで構築した場合は、次のように表示されます。

![Time Series Insights エクスプローラーの期間設定](media/parameterized-url/url1.png)

完全なビュー (グラフを含む) は次のようになります。

![グラフ ビュー](media/parameterized-url/url2.png)

## <a name="next-steps"></a>次のステップ
[C# を使用してデータのクエリを実行する](time-series-insights-query-data-csharp.md)
