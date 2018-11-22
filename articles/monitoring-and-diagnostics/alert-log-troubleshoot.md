---
title: Azure Monitor におけるログ アラートのトラブルシューティング
description: Azure におけるログ アラート ルールに関する一般的な問題、エラー、解決方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 68488788f73c9662b5d1eaa3b670f2120941defc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616488"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Azure Monitor におけるログ アラートのトラブルシューティング  
## <a name="overview"></a>概要
この記事では、Azure Monitor 内でログ アラートを設定しているときに遭遇する一般的な問題の解決方法について説明します。 また、ログ アラートの機能や構成に関してよく寄せられる質問の解決策を紹介しています。 

アラートを説明する**ログ アラート**という用語。このアラートは、[Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) または [Application Insights](../application-insights/app-insights-analytics.md) においてカスタム クエリに基づいて作動します。 機能、用語、および種類については、「[ログ アラート - 概要](monitor-alerts-unified-log.md)」をご覧ください。

> [!NOTE]
> この記事では、トリガーされたアラート ルールが Azure portal に表示されるケースや、関連するアクション グループによって実行された通知が Azure portal に表示されるケースについては考慮していません。 そのようなケースについて詳しくは、[アクション グループ](monitoring-action-groups.md)に関する記事を参照してください。


## <a name="log-alert-didnt-fire"></a>ログ アラートが作動しない

ここでは、構成された [Azure Monitor のログ アラート ルール](alert-log.md)の状態が、[予期された場合に "*起動済み*"](monitoring-alerts-managing-alert-states.md) にならない共通の理由をいくつか示します。 

### <a name="data-ingestion-time-for-logs"></a>ログのデータ インジェストの時間
ログ アラートは、[Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) または [Application Insights](../application-insights/app-insights-analytics.md) に基づいてクエリを定期的に実行します。 Log Analytics は、世界中の多様なソースから数千のお客様によって送られた何テラバイトものデータを処理するため、さまざまな時間的遅延の影響を受けやすいサービスとなっています。 詳細については、「[Log Analytics のデータ インジェスト時間](../log-analytics/log-analytics-data-ingestion-time.md)」を参照してください。

システムは、データ インジェストの遅延を軽減するため、必要なデータが取り込まれていないことがわかると、待機してアラート クエリを何度も再試行します。 システムによって、指数関数的に増加する待機時間が設定されます。 ログ アラートはデータが使用可能にならないとトリガーされません。したがって、遅延の原因はログ データ インジェストが遅いことによります。 

### <a name="incorrect-time-period-configured"></a>構成されている期間が正しくない
[ログ アラートの用語](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)に関する記事で説明されているように、クエリの時間範囲は、構成に記述された期間によって指定されます。 クエリから返されるのは、この時間範囲内に作成されたレコードだけです。 期間では、ログ クエリ用にフェッチされるデータを制限して不正使用を防いだり、ログ クエリで使用されている時間コマンド (ago など) を回避することができます。 
"*たとえば、期間が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが、ログ クエリで使用されます。ログ クエリが *ago (1d)* のような時間コマンドを使用する場合でも、クエリは午後 12 時 15 分から午後 1 時 15 分までのデータしか使用しません。期間がその間隔に設定されているためです。*"

したがって、構成内の期間がクエリと一致しているかを確認してください。 前出の例で、緑色のマーカーで示したようにログ クエリに *ago (1d)* を使用した場合、クエリが意図どおりに実行されるようにするには、期間が 24 時間または 1,440 分 (赤色で示した箇所) に設定されている必要があります。

![期間](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>[アラートを表示しない] オプションが設定されている
[Azure portal でのログ アラート ルールの作成](alert-log.md#managing-log-alerts-from-the-azure-portal)に関する記事の手順 8 で説明しているように、ログ アラートでは、構成された期間にトリガーや通知アクションを抑止するために **[アラートを表示しない]** オプションが提供されます。 この結果、アラートが実際に発生して抑止されたときに、アラートが発生しなかったと受け取る場合があります。  

![アラートを表示しない](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>メトリック測定のアラート ルールが正しくない
**メトリック測定ログ アラート**は、ログ アラートのサブタイプです。特別な機能を備え、アラートのクエリ構文が制限されています。 メトリック測定のログ アラート ルールでは、クエリ出力がメトリック時系列、すなわち同じ長さの個々の期間とそれに対応する集計値を含むテーブルであることが必要です。 また、ユーザーは、そのテーブルに AggregatedValue と一緒に追加の変数を含めることができます。 これらの変数はテーブルを並べ替えるために使用できます。 

たとえば、メトリック測定のログ アラート ルールが次のように構成されていたとします。
- クエリ: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 期間: 6 時間
- しきい値: 50
- アラート ロジック: 連続 3 回の違反
- 集計基準: $table を選択

コマンドには *summarize … by* が含まれ、2 つの変数 (timestamp & $table) が指定されているため、システムは集計基準として $table を選択します。 基本的には結果テーブルが *$table* フィールドで並べ替えられます。その後、テーブルの種類 (availabilityResults など) ごとに複数の AggregatedValue に注目し、連続 3 回以上の違反が存在するかどうかを確認します。

![複数の値を使用したメトリック測定クエリの実行](./media/monitor-alerts-unified/LogMMQuery.png)

"集計基準" は $table であるため、データが $table 列で並べ替えられた (赤色で示した箇所) 後、"集計基準" フィールド ($table) の種類をグループ化して探します。たとえば、availabilityResults の値は、1 つのプロット/エンティティとして見なされます (オレンジ色で示した箇所)。 アラート サービスは、この値プロット/エンティティから、テーブル値 "availabilityResults" に対してアラートがトリガーされる連続 3 回の違反 (緑色で示した箇所) がないかを確認します。 同様に、$table のその他すべての値に関して、連続 3 回の違反が見つかった場合、同じものに対して別のアラート通知がトリガーされます。アラート サービスは、1 つのプロット/エンティティに含まれる各値 (オレンジ色で示した箇所) を時刻別に自動的に並べ替えます。

ここで、メトリック測定ログ アラート ルールが変更され、クエリが `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` になったとしましょう。残りの構成は、連続 3 回の違反に対するアラート ロジックも含め、前回と同じままになります。 この場合の "集計基準" オプションは、既定で timestamp になります。 summarize…by のクエリに指定された値が 1 つ (timestamp) しかないため、前述の例と同様、実行終了時点の出力は以下のようになります。 

   ![単一の値を使用したメトリック測定クエリの実行](./media/monitor-alerts-unified/LogMMtimestamp.png)

"集計基準" は timestamp であるため、データが timestamp 列で並べ替えられた (赤色で示した箇所) 後、timestamp でグループ化します。たとえば、`2018-10-17T06:00:00Z` の値は、1 つのプロット/エンティティとして見なされます (オレンジ色で示した箇所)。 この値プロット/エンティティでは、連続する違反がアラート サービスによって検出されることはなく (それぞれのタイムスタンプ値にはエントリが 1 つしかないため)、アラートは決してトリガーされることはありません。 このような場合、ユーザーは次のいずれかを実行する必要があります。
- ダミー変数または ($table のような) 既存の変数を追加して、構成した "集計基準" フィールドを使用して並べ替えが正しく行われるようにします。
- (または) 適宜 "*違反の合計数*" に基づくアラート ロジックを使用するようアラート ルールを再構成します。
 
## <a name="log-alert-fired-unnecessarily"></a>ログ アラートが不必要に作動する
構成されている [Azure Monitor のログ アラート ルール](alert-log.md)が、[Azure アラート](monitoring-alerts-managing-alert-states.md)で確認すると、発生すべきでないタイミングでトリガーされていることがあります。以降、その一般的な理由について詳しく取り上げます。

### <a name="alert-triggered-by-partial-data"></a>一部のデータによってアラートがトリガーされる
Log Analytics と Application Insights を支えている Analytics は、インジェストの遅延や処理の影響を受けます。このため、指定されたログ アラート クエリが実行されるタイミングで、利用できるデータがまったく存在しない場合や一部のデータしか利用できない場合があります。 詳細については、「[Log Analytics のデータ インジェスト時間](../log-analytics/log-analytics-data-ingestion-time.md)」を参照してください。

アラート ルールの構成方法によっては、アラートの実行時点でログのデータが存在しない場合や不十分な場合に誤作動が生じる可能性があります。 そのような場合は、アラート クエリまたは構成を変更することをお勧めします。 *たとえば、分析クエリから返される結果の数が 5 件未満のときにトリガーするようログ アラート ルールが構成されている場合、データが存在しないとき (0 レコード) または一部の結果がある (1 レコード) ときに、アラート ルールがトリガーされます。一方、インジェストの遅延後に Analytics で同じクエリを実行すると、すべてのデータが対象となり、結果は 10 レコードとして返されます。*

### <a name="alert-query-output-misunderstood"></a>アラート クエリの出力が誤って解釈される
ログ アラートでは、アラートのロジックをユーザーが分析クエリを使用して指定します。 指定する分析クエリには、さまざまなビッグ データや数学関数を使用して、特定のコンストラクトを形成することができます。 アラート サービスは、ユーザーが指定したクエリを、データと共に指定された間隔で、指定された期間にわたって実行します。そのため、指定したクエリには、選択したアラートの種類に応じて若干の変更が加えられます。同じことは、以下の図に示すように、[シグナル ロジックの構成] 画面の [実行するクエリ] セクションで確認できます。![実行するクエリ](./media/monitor-alerts-unified/LogAlertPreview.png)
 
**[実行するクエリ]** セクションには、ログ アラート サービスによって実行される内容が表示されます。そのため、アラート クエリの出力がどのようになるかをアラートの作成前に把握したい場合、ユーザーは、ここに表示されているクエリ (期間を含む) を [Analytics ポータル](../log-analytics/log-analytics-log-search-portals.md)または [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) で実行することができます。
 
## <a name="next-steps"></a>次の手順

* [Azure アラートのログ アラート](monitor-alerts-unified-log.md)について学習します。
* [Application Insights](../application-insights/app-insights-analytics.md) についてさらに学習します。
* [Log Analytics](../log-analytics/log-analytics-overview.md) についてさらに学習します。 

