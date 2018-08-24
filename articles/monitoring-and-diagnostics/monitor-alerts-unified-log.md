---
title: Azure Monitor でのログ アラート
description: Azure アラートに関して指定した分析クエリ条件が満たされたときに、電子メール、通知、Web サイト URL (webhook)の呼び出し、またはオートメーションをトリガーします。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: fd1fb978fb47c69b2eb672bc27baee73dfdd0a29
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142891"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Azure Monitor でのログ アラート - Alerts 
この記事では、ログ アラートの詳細について説明します。ログ アラートは、新しい [Azure アラート](monitoring-overview-unified-alerts.md)でサポートされるアラートの一種です。これを使用すると、Azure の分析プラットフォームをアラートの発信基準として使用できます。


ログ アラートは、[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) または [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 用に作成されたログ検索ルールで構成されます。 ログ アラートの価格詳細は「[Azure Monitor の価格](https://azure.microsoft.com/en-us/pricing/details/monitor/)」ページにあります。 Azure 請求書には、ログ アラートが型 `microsoft.insights/scheduledqueryrules` として表示され、さらに次が表示されます。
- Application Insights のログ アラートが正しいアラート名でリソース グループとアラート プロパティと共に表示されます
- Log Analytics のログ アラートがアラート名 (`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` として) でリソース グループとアラート プロパティと共に表示されます

    > [!NOTE]
    > Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。 `<, >, %, &, \, ?, /` のような無効な文字が使用された場合、請求書では `_` で置換されます。

## <a name="log-search-alert-rule---definition-and-types"></a>ログ検索アラート ルールの定義と種類

ログ検索ルールは、指定されたログ クエリを一定の間隔で自動的に実行するために、Azure アラートによって作成されます。  ログ クエリの結果が特定の条件に一致すると、アラート レコードが作成されます。 ルールではその後、[アクション グループ](monitoring-action-groups.md)を使用して、1 つ以上のアクションを自動的に実行できます。 

ログ検索ルールは次の内容で定義されます。
- **ログ クエリ**:  警告ルールが実行されるたびに実行されるクエリ。  このクエリから返されるレコードは、アラートを作成するかどうかの決定に使用されます。 *Azure Application Insights* のクエリには、[アプリケーション間呼び出し](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)を含めることもできます (ユーザーが外部アプリケーションへのアクセス権を持っていル場合)。 

    > [!IMPORTANT]
    > [Application Insights 用のアプリケーション間クエリ](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)のサポートはプレビュー段階であり、2 つ以上のアプリでの使用が制限されている機能とユーザー エクスペリエンスは変更されることがあります。 [ワークスペース間クエリ](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery)と [Log Analytics 用のリソース間クエリ](../log-analytics/log-analytics-cross-workspace-search.md)は、現在 Azure アラートでは**サポートされていません**。

- **期間**。  クエリの時間範囲を指定します。 クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。 期間では、ログ クエリ用にフェッチされるデータを制限して不正使用を防いだり、ログ クエリで使用されている時間コマンド (ago など) を回避することができます。 <br>*たとえば、期間が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが、ログ クエリの実行用に返されます。ログ クエリで「ago (7d)」などの時間コマンドが使用されている場合、そのログ クエリは午後 12 時 15 分から午後 1 時 15 分までの間のデータのみを対象に実行されます。つまり、過去 60 分間についてのみデータが存在し、ログ クエリで指定された 7 日間についてはデータが存在しないかのように動作します。*
- **[頻度]**:   クエリの実行頻度を指定します。 5 分から 24 時間までの値を指定できます。 この値は、期間の値以下にする必要があります。  この値が期間の値よりも大きい場合、レコードを見落とすおそれがあります。<br>*たとえば、期間が 30 分、頻度が 60 分であるとします。クエリが午後 1 時に実行された場合、午後 12 時 30 分から午後 1 時までの間のレコードが返されます。次回クエリが実行されるのは午後 2 時であり、このときには午後 1 時 30 分から午後 2 時までの間のレコードが返されます。つまり、午後 1 時から午後 1 時 30 分までの間に作成されたレコードは評価されないことになります。*
- **しきい値**:   ログ検索の結果を評価し、アラートの生成が必要であるかどうかを判定するための値です。  しきい値は、ログ検索アラート ルールの種類によって異なります。

ログ検索ルールには、[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 用と [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 用のいずれについても、2 つの種類があります。 どちらについても、後のセクションで詳しく説明します。

- **[結果の数](#number-of-results-alert-rules)**。 ログ検索によって返されるレコードの数が指定された数を超えた場合に、アラートが 1 回生成されます。
- **[メトリック測定](#metric-measurement-alert-rules)**。  ログ検索の結果の値が指定されたしきい値を超えた場合に、オブジェクトごとにアラートが生成されます。

この 2 種類のアラート ルールの違いは次のとおりです。

- *結果の数*アラート ルールが常に 1 つのアラートを作成するのに対して、*メトリック測定*アラート ルールは、しきい値を超えたオブジェクトごとにアラートを作成します。
- *結果の数*の警告ルールでは、1 回しきい値を超えた時点で警告が生成されます。 これに対して、*メトリック測定*のアラート ルールでは、一定期間内にしきい値を一定回数超過した場合に、アラートが生成されます。

### <a name="number-of-results-alert-rules"></a>結果の数のアラート ルール
**結果の数**のアラート ルールでは、検索クエリによって返されるレコード数が指定されたしきい値を超えた場合に、1 回だけアラートが生成されます。 この種類のアラート ルールは、Windows イベント ログ、Syslog、WebApp 応答、カスタム ログなどのイベントを操作するために最適です。  特定のエラー イベントが作成されたとき、または特定の期間内に複数のエラー イベントが作成されたときなどに、アラートを作成することができます。

**しきい値**: 結果の数アラート ルールのしきい値は特定の値より大きいか、または小さいかのどちらかです。  ログ検索によって返されるレコード数がこの条件を満たしたときに、アラートが生成されます。

1 つのイベントに関するアラートを生成するには、結果の数を 0 より大きい値に設定し、クエリが最後に実行されてから作成された 1 つのイベントの発生をチェックします。 一部のアプリケーションでは、必ずしもアラートを発生させない偶発的なエラーが記録される場合もあります。  たとえば、エラー イベントを作成したプロセスをアプリケーションが再試行し、次回は成功する場合などがあります。  このような場合は、特定の期間内に複数のイベントが作成されない限り、アラートを作成しないようにできます。  

また、イベントが発生しないときにアラートを作成する場合もあります。  たとえば、正しく動作していることを示すために定期的なイベントを記録するプロセスがあります。  そのようなプロセスが特定の期間内にそれらのイベントを記録しなかった場合には、アラートを作成する必要があります。  この場合、しきい値を **1 より小さく**設定します。

#### <a name="example"></a>例
Web ベースのアプリがコード 500 (つまり) 内部サーバー エラーの応答をいつユーザーに提供するかを確認するシナリオを考えてみます。 以下のような詳細情報を使用してアラート ルールを作成します。  
- **クエリ:** 要求 | ここで resultCode == "500"<br>
- **期間:** 30 分<br>
- **アラート頻度:** 5 分<br>
- **しきい値:** 0 より大きい<br>

その後、アラートはこのクエリを 30 分のデータで 5 分ごとに実行して、結果コードが 500 であったすべてのレコードを探します。 このようなレコードが 1 つでも見つかると、アラートが生成され、構成されたアクションがトリガーされます。

### <a name="metric-measurement-alert-rules"></a>メトリック測定のアラート ルール

- **メトリック測定**のアラート ルールでは、クエリの対象となったオブジェクトが分析され、特定の値が指定されたしきい値を上回っているオブジェクトについて、それぞれ別個にアラートが生成されます。  **結果の数**のアラート ルールとは、以下の点が明確に異なります。
- **集計関数**: 実行する計算と、集計対象になる可能性のある数値フィールドを決定します。  たとえば、**count()** であれば、クエリで指定したレコードの件数が返されます。**avg(CounterValue)** であれば、一定期間内の CounterValue フィールドの平均値が返されます。 クエリの集計関数を指定/呼び出す必要があります (AggregatedValue と数値を指定します)。 
- **グループ フィールド**: このフィールドのインスタンスごとに値を集計したレコードが作成されます。アラートは、それぞれのインスタンスについて生成されます。  たとえば、コンピューターごとにアラートを生成する場合は、**by Computer** を使用します。 

    > [!NOTE]
    > Application Insight に基づくメトリック測定アラート ルールでは、データをグループ化するためのフィールドを指定できます。 これを行うには、ルール定義の **[集計]** オプションを使用します。   
    
- **間隔**:  データを集計する間隔を定義します。  たとえば、**5 分**を指定した場合には、アラートに対して指定した期間にわたり、グループ フィールドの各インスタンスについて、5 分間隔でレコードが作成されます。

    > [!NOTE]
    > クエリでは Bin 関数を使用して間隔を指定する必要があります。 bin() では不均一な時間間隔が返される場合があるため、実行時、アラートは適切な時間を使用して bin を自動的に bin_at コマンドに変換し、固定小数点を持つ結果が得られるようにします
    
- **しきい値**: メトリック測定アラート ルールのしきい値は、集計値と違反数によって定義されます。  ログ検索でいずれかのデータ ポイントが一定の値を超えると、抵触が 1 回発生したと判定されます。  そして、結果に含まれるオブジェクトの抵触の発生回数が指定された値を超えたときに、そのオブジェクトについて警告が生成されます。

#### <a name="example"></a>例
いずれかのコンピューターでプロセッサの使用率が 90% を超える状態が 30 分間に 3 回発生した場合にアラートを生成するシナリオを考えてみましょう。  以下のような詳細情報を使用してアラート ルールを作成します。  

- **[クエリ]:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **期間:** 30 分<br>
- **アラート頻度:** 5 分<br>
- **集計値:** 90 よりも大きい<br>
- **アラートをトリガーする基準:** 違反総数が 2 より大<br>

このクエリは、各コンピューターの平均値を 5 分間隔で算出します。  このクエリは過去 30 分間に収集されたデータを対象に、5 分ごとに実行されます。  コンピューターが 3 台の場合、サンプル データは以下のようになります。

![サンプル クエリの結果](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

この例では、指定された期間内に srv02 と srv03 の 2 台がしきい値 (90 %) に合計 3 回抵触しています。このため、この 2 台について個別にアラートが生成されます。  **[アラートをトリガーする基準]** を **[Consecutive (連続)]** に変更した場合、3 回連続でしきい値に違反した srv03 についてのみアラートが生成されます。


## <a name="log-search-alert-rule---creation-and-modification"></a>ログ検索アラート ルールの作成と変更

ログ アラートとそれを構成するログ検索アラート ルールは、次の手段で表示、作成、変更できます。
- Azure ポータル
- REST API (PowerShell 経由を含む)
- Azure Resource Manager のテンプレート

### <a name="azure-portal"></a>Azure ポータル
[新しい Azure アラート](monitoring-overview-unified-alerts.md)が導入されて以来、ユーザーは Azure portal を使用して、すべての種類のアラートを同じ使用手順で一元的に管理できるようになりました。 詳しくは、[新しい Azure アラートの使用](monitor-alerts-unified-usage.md)に関する記事をご覧ください。

ユーザーはまた、Azure の任意の Analytics プラットフォームでクエリを完成させてから、*そのクエリを保存することによって、Alerts で使用するためにインポート*できます。 次の手順に従います。
- *Application Insights の場合*: Analytics ポータルに移動し、クエリとその結果を検証します。 次に、一意の名前で *[Shared Queries] (共有クエリ)* に保存します。
- *Log Analytics の場合*: ログ検索に移動し、クエリとその結果を検証します。 次に、一意の名前で任意のカテゴリに保存します。

その後、[Alerts でログ アラートを作成](monitor-alerts-unified-usage.md)すると、下の例に示すように、保存されたクエリがシグナルの種類 **[Log (Saved Query)] (ログ (保存されたクエリ))** として一覧表示されます: ![Alerts にインポートされた保存されたクエリ](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Alerts へのインポートでの **[Log (Saved Query)] (ログ (保存されたクエリ))** の結果の使用。 そのため、Analytics でその後に実行されたどの変更もログ検索アラート ルールには反映されず、その逆も同様です。

### <a name="rest-apis"></a>REST API
ログ アラート用に提供されている API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 そのため、PowerShell や、この API を使用するその他のオプションによってもアクセスできます。

REST API の使用に関する詳細と例については、次の記事をご覧ください。
- [Log Analytics アラートの REST API に関する記事](../log-analytics/log-analytics-api-alerts.md) -  Azure Log Analytics 用のログ検索アラート ルールを作成、管理する方法
- [Azure Monitor の Scheduled Query Rules REST API に関する記事](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) - Azure Application Insights 用のログ検索アラート ルールを作成、管理する方法

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
ユーザーはまた、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の柔軟な機能を使って、リソースの作成や更新を行い、ログ アラートの作成や更新に使用することができます。

Resource Manager テンプレートの使用に関する詳細と例については、次の記事をご覧ください。
- Azure Log Analytics に基づくログ アラートの[保存済み検索とアラート管理](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics)に関する記事
- Azure Application Insights に基づくログ アラートの[スケジュール済みクエリ ルール](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights)に関する記事
 

## <a name="next-steps"></a>次の手順
* [Azure のログ アラートの Webhook](monitor-alerts-unified-log-webhook.md) について理解する。
* 新しい [Azure アラート](monitoring-overview-unified-alerts.md)について学習する。
* [Application Insights](../application-insights/app-insights-analytics.md) についてさらに学習します。
* [Log Analytics](../log-analytics/log-analytics-overview.md) についてさらに学習します。    
