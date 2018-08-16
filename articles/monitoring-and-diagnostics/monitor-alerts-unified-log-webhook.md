---
title: Azure Alerts でのログ アラートのための webhook アクション | Microsoft Docs
description: この記事では、Log Analytics または Application Insights を使用するログ アラート ルールでデータを HTTP webhook としてプッシュする方法と、さまざまなカスタマイズ例の詳細について説明します。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f20e102ee1d100ea02da53fe460b56f8f8390418
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426695"
---
# <a name="webhook-actions-for-log-alert-rules"></a>ログ アラート ルールの webhook アクション
[Azure でアラートを作成する](monitor-alerts-unified-usage.md)際に、1 つ以上のアクションを実行する[アクション グループの使用を構成する](monitoring-action-groups.md)ことができます。  この記事では、使用できるさまざまな webhook アクションと、カスタム JSON ベース webhook の構成に関する詳細を示します。


## <a name="webhook-actions"></a>Webhook アクション

Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。  呼び出されるサービスは、Webhook をサポートし、受信したペイロードの使用方法を決定できる必要があります。   アラートに対する応答で webhook を使用する例では、[Slack](http://slack.com) でメッセージを送信したり、[PagerDuty](http://pagerduty.com/) でインシデントを作成しています。  

webhook アクションには、次の表に示すプロパティが必要です。

| プロパティ | 説明 |
|:--- |:--- |
| Webhook URL |Webhook の URL。 |
| Custom JSON payload (カスタム JSON ペイロード) |アラート作成中にこのオプションを選択するときに、webhook と共に送信するカスタム ペイロード。 詳細については、[Azure アラートを使用したアラートの管理](monitor-alerts-unified-usage.md)に関するページを参照してください。 |

> [!NOTE]
> ログ アラートの *[webhook 用のカスタム Json ペイロードを含む]* オプションの横にある [webhook のテスト] ボタンをクリックすると、webhook URL をテストするためのダミーの呼び出しがトリガーされます。 この呼び出しには、ログ アラートに使用される実際のデータや JSON スキーマの表現は含まれません。 

Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。  既定では、ペイロードには次の表の値が含まれます。このペイロードを独自のカスタム値で置き換えることができます。  その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めることができます。


| パラメーター | 可変 | 説明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |アラート ルールの名前。 |
| severity |#severity |起動されたログ アラートに設定されている重大度。 |
| AlertThresholdOperator |#thresholdoperator |アラート ルールのしきい値演算子。  "*Greater than*" または "*Less than*" を使用できます。 |
| AlertThresholdValue |#thresholdvalue |アラート ルールのしきい値。 |
| LinkToSearchResults |#linktosearchresults |アラートを作成したクエリからのレコードを返す Analytics ポータルへのリンク。 |
| ResultCount |#searchresultcount |検索結果に含まれるレコードの数。 |
| Search Interval End time |#searchintervalendtimeutc |UTC でのクエリの終了時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 |
| Search Interval |#searchinterval |アラート ルールの時間枠。フォーマットは HH:mm:ss です。 |
| Search Interval StartTime |#searchintervalstarttimeutc |UTC でのクエリの開始時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 
| SearchQuery |#searchquery |アラート ルールで使用されるログ検索クエリ。 |
| SearchResults |"IncludeSearchResults": true|クエリで JSON テーブルとして返されるレコード。上限は最初の 1,000 レコード。"IncludeSearchResults":true がカスタム JSON webhook 定義に最上位レベルのプロパティとして追加されている場合。 |
| WorkspaceID |#workspaceid |Log Analytics ワークスペースの ID |
| アプリケーション ID |#applicationid |Application Insight アプリの ID。 |
| サブスクリプション ID |#subscriptionid |Application Insights で使用する Azure サブスクリプションの ID。 

> [!NOTE]
> Analytics セクションで表示するために、LinkToSearchResults により、URL の SearchQuery、Search Interval StartTime、Search Interval EndTime などのパラメーターが Azure portal に渡されます。 Azure portal の URI サイズの上限は約 2,000 文字であり、パラメーター値がこの上限を超えると開きます。 ユーザーは手動で詳細を入力して Analytics ポータルで結果を表示したり、[Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) または [Log Analytics REST API](https://dev.loganalytics.io/reference) を使用して結果をプログラミングで取得したりできます。 

たとえば、 *text*という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。  この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
この例のペイロードは、Webhook に送信されると、次のような内容に解決されます。

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
カスタム webhook 内のすべての変数は、"#searchinterval" のように JSON エンクロージャ内で指定する必要があるため、結果の webhook にも、エンクロージャ内に "00:05:00" のような変数データが含まれることになります。

カスタム ペイロードに検索結果を含めるには、**IncudeSearchResults** が JSON ペイロードの最上位レベルのプロパティとして設定されていることを確認します。 

## <a name="sample-payloads"></a>サンプル ペイロード
このセクションでは、ペイロードが標準の場合やカスタムの場合など、ログ アラートの webhook のサンプル ペイロードを紹介します。

> [!NOTE]
> 旧バージョンとの互換性を確保するために、Azure Log Analytics を使用するアラートの標準 webhook ペイロードは、[Log Analytics のアラート管理](../log-analytics/log-analytics-alerts-creating.md)と同じです。 ただし、[Application Insights](../application-insights/app-insights-analytics.md) を使用するログ アラートの場合、標準 webhook ペイロードはアクション グループ スキーマに基づいています。

### <a name="standard-webhook-for-log-alerts"></a>ログ アラートの標準 webhook 
以下のサンプルはどちらも、2 つの列と 2 つの行のみで構成されたダミー ペイロードを示しています。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log Analytics のログ アラート
以下は、Log Analytics ベースのログ アラートに使用される場合の、*カスタム JSON オプションが含まれていない*標準 webhook アクションのサンプル ペイロードです。

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights のログ アラート
以下は、Application Insights ベースのログ アラートに使用される場合の、*カスタム JSON オプションが含まれていない*標準 webhook のサンプル ペイロードです。
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>カスタム JSON ペイロードを使用したログ アラート
たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、次のように入力します。 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下は、どのログ アラートでも使用できるカスタム webhook アクションのサンプル ペイロードです。
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>次の手順
- [Azure Alerts のログ アラート](monitor-alerts-unified-log.md)について確認します。
- [Azure でのアクション グループ](monitoring-action-groups.md)の作成および管理
- [Application Insights](../application-insights/app-insights-analytics.md) についてさらに学習します。
- [Log Analytics](../log-analytics/log-analytics-overview.md) についてさらに学習します。 