---
title: "Azure アラートでのログ アラートの webhook アクション (プレビュー) | Microsoft Docs"
description: "この記事では、Log Analytics または Application Insights を使用するログ アラート ルールでデータを HTTP webhook としてプッシュする方法と、さまざまなカスタマイズ例の詳細について説明します。"
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>ログ アラート ルールの webhook アクション
[Azure でアラートを作成する (現時点ではプレビュー)](monitor-alerts-unified-usage.md) 際に、1 つ以上のアクションを実行する[アクション グループの使用を構成する](monitoring-action-groups.md)ことができます。  この記事では、使用できるさまざまな webhook アクションと、カスタム JSON ベース webhook の構成に関する詳細を示します。


## <a name="webhook-actions"></a>Webhook アクション

Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。  呼び出されるサービスは、Webhook をサポートし、受信したペイロードの使用方法を決定できる必要があります。  また、要求に API で認識される形式を使用すれば、Webhook を明示的にはサポートしない REST API も呼び出すことができます。  アラートに対する応答で webhook を使用する例では、[Slack](http://slack.com) でメッセージを送信したり、[PagerDuty](http://pagerduty.com/) でインシデントを作成しています。  Webhook で Slack を呼び出すアラート ルールを作成する詳細なチュートリアルについては、[Log Analytics のアラートでの Webhook](../log-analytics/log-analytics-alerts-webhooks.md)に関する記事を参照してください。

webhook アクションには、次の表に示すプロパティが必要です。

| プロパティ | [説明] |
|:--- |:--- |
| Webhook URL |Webhook の URL。 |
| Custom JSON payload (カスタム JSON ペイロード) |Webhook と共に送信するカスタム ペイロードです。  アラートの作成時にオプションが選択された場合。 詳細については、[Azure アラートを使用したアラートの管理 (プレビュー)](monitor-alerts-unified-usage.md) に関するページをご覧ください。 |

> [!NOTE]
> ログ アラートの *[webhook 用のカスタム Json ペイロードを含む]* オプションの横にある [webhook のテスト] ボタンをクリックすると、webhook URL をテストするためのダミーの呼び出しがトリガーされます。 この呼び出しには、ログ アラートに使用される実際のデータや JSON スキーマの表現は含まれません。 カスタム JSON 表現でどの webhook でもテストできますが、アクション グループで構成されているすべての webhook がカスタム JSON ペイロードで送信されます。

Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。  既定では、ペイロードには次の表に示す値が格納されます。  このペイロードは、独自のカスタム ペイロードに置き換えることができます。  その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めることができます。


| パラメーター | 変数 | [説明] |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |アラート ルールの名前。 |
| AlertThresholdOperator |#thresholdoperator |アラート ルールのしきい値演算子。  "*Greater than*" または "*Less than*" を使用できます。 |
| AlertThresholdValue |#thresholdvalue |アラート ルールのしきい値。 |
| LinkToSearchResults |#linktosearchresults |アラートを作成したクエリに基づいてレコードを返す Log Analytics ログ検索へのリンク。 |
| ResultCount |#searchresultcount |検索結果に含まれるレコードの数。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |UTC 形式で記述したクエリの終了時刻。 |
| SearchIntervalInSeconds |#searchinterval |アラート ルールの時間枠。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |UTC 形式で記述したクエリの開始時刻。 |
| SearchQuery |#searchquery |アラート ルールで使用されるログ検索クエリ。 |
| SearchResults |"IncludeSearchResults":true|クエリで JSON テーブルとして返されるレコード。上限は最初の 1,000 レコード/行。"IncludeSearchResults":true がカスタム JSON webhook 定義に最上位レベルのプロパティとして追加されている場合。 |
| WorkspaceID |#workspaceid |Log Analytics ワークスペースの ID |
| 重大度 |#severity |起動されたログ アラートに設定されている重大度。 |

たとえば、 *text*という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。  この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

この例のペイロードは、Webhook に送信されると、次のような内容に解決されます。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

カスタム ペイロードに検索結果を含めるには、**IncudeSearchResults** が JSON ペイロードの最上位レベルのプロパティとして設定されていることを確認します。


Webhook で外部サービスを開始するアラート ルールを作成する例の詳細については、[Log Analytics で アラート webhook アクションを作成して、メッセージを Slack に送信する](../log-analytics/log-analytics-alerts-webhooks.md)方法に関する記事を参照してください。

## <a name="sample-payload"></a>サンプル ペイロード
このセクションでは、ペイロードが標準の場合やカスタムの場合など、ログ アラートの webhook のサンプル ペイロードを紹介します。

> [!NOTE]
> 旧バージョンとの互換性を確保するために、Azure Log Analytics を使用するアラートの標準 webhook ペイロードは、[Operations Management Suite のアラート管理](../log-analytics/log-analytics-solution-alert-management.md)と同じです。 ただし、[Application Insights](../application-insights/app-insights-analytics.md) を使用するログ アラートの場合、標準 webhook ペイロードはアクション グループ スキーマに基づいています。

### <a name="standard-webhook-for-log-alerts"></a>ログ アラートの標準 webhook
以下のサンプルはどちらも、2 つの列と 2 つの行のみで構成されたダミー ペイロードを示しています。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log Analytics のログ アラート
以下は、Log Analytics ベースのログ アラートに使用される場合の、カスタム JSON が含まれていない標準 webhook アクションのサンプル ペイロードです。

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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights のログ アラート
以下は、Application Insights ベースのログ アラートに使用される場合の、カスタム JSON が含まれていない標準 webhook アクションのサンプル ペイロードです。


    {
    "schemaId":"LogAlert","data":
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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>カスタム JSON ペイロードを使用したログ アラート
たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、次のように入力します。

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

以下は、どのログ アラートでも使用できるカスタム webhook アクションのサンプル ペイロードです。


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>次の手順
- [Azure でのアクション グループ](monitoring-action-groups.md)の作成および管理
- [Azure Alerts (プレビュー) のログ アラート](monitor-alerts-unified-log.md)の詳細をご覧ください
