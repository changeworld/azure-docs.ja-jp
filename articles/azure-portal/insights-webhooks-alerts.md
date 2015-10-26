<properties
	pageTitle="他のシステムに送信するように Azure アラートを構成する方法"
	description="他の Azure 以外のシステムに Azure アラートを再ルーティングします。"
	authors="rboucher"
	manager="ronmart"
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="robb"/>

# アラート用 Webhook の構成方法

Webhook では、ユーザーは、後処理のために他のシステムに Azure アラート通知をルーティングしたり、カスタム通知をルーティングしたりすることできます。たとえば、SMS の送信、バグのログ記録、チャット/メッセージング サービスなどを介したチームへの通知に関する受信 Web 要求を処理できるサービスにアラートをルーティングできます。

Webhook URI は有効な HTTP または HPPTS エンドポイントである必要があります。Azure アラート サービスは、指定された Webhook で POST 操作を行い、特定の JSON ペイロードとスキーマを渡します。

## ポータルを使用して Webhook を構成する

[Azure ポータル](https://portal.azure.com/)のアラートの作成および更新画面では、Webhook URI を追加または更新することができます。

![アラート ルールの追加](./media/insights-webhooks-alerts/Alertwebhook.png)


## 認証

認証には次の 2 種類があります。

1. **トークン ベースの認証** - この場合、**https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue* などのトークン ID を持つ Webhook URI を保存します。
2.	**基本認証** - userid および password を使用: この場合は、**https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar* などの Webhook URI を保存します。

## ペイロード スキーマ

POST 操作には、すべてのメトリック ベース アラートの以下の JSON ペイロードとメトリックが含まれます。

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE]次の更新で、イベント ("conditionType": "Event") に対するアラートのサポートを追加します。


| フィールド | 必須 | 値の固定セット | メモ |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|"Activated"、"Resolved"|これは、アラートの種類を確認する方法です。Azure は、各条件セットに対するアクティブ化されたアラートおよび解決済みのアラートを自動的に送信します。|
|context| Y | | アラート コンテキスト|
|timestamp| Y | | アラートがトリガーされた時刻。メトリックが診断ストレージから読み取られるとすぐに、アラートがトリガーされます。|
|id | Y | | すべてのアラート ルールには一意の ID があります。|
|name|Y | |
|description |Y | |アラートについての説明。|
|conditionType |Y |"Metric"、"Event" |2 つの種類のアラートがサポートされています。1 つはメトリックに基づき、もう 1 つはイベントに基づきます。将来的には、イベントのアラートをサポートする予定です。この値を使用することで、アラートがメトリックとイベントのどちらに基づくものであるかを確認できるようになります。|
|condition |Y | |これには、conditionType に基づいて確認するための特定のフィールドがあります。|
|metricName |メトリック アラートの場合 | |ルールによる監視対象を定義するメトリックの名前。|
|metricUnit |メトリック アラートの場合 |"Bytes"、"BytesPerSecond"、"Count"、"CountPerSecond"、"Percent"、"Seconds"|	 メトリックで使用できる単位。使用できる値: https://msdn.microsoft.com/ja-jp/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |メトリック アラートの場合 | |アラートの原因となったメトリックの実際の値|
|threshold |メトリック アラートの場合 | |アラートをアクティブ化するしきい値の値。|
|windowSize |メトリック アラートの場合 | |しきい値に基づいてアラート アクティビティを監視するために使用される期間。5 分から 1 日の間である必要があります。ISO 8601 期間形式。|
|timeAggregation |メトリック アラートの場合 |"Average"、"Last"、"Maximum"、"Minimum"、"None"、"Total" |	収集されたデータの経時的な結合方法。既定値は Average です。使用できる値: https://msdn.microsoft.com/ja-jp/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |メトリック アラートの場合 | |データとしきい値を比較するために使用する演算子。|
|subscriptionId |Y | |Azure サブスクリプション GUID|
|resourceGroupName |Y | |影響を受けるリソースのリソース グループ名|
|resourceName |Y | |影響を受けるリソースのリソース名|
|resourceType |Y | |影響を受けるリソースの種類|
|resourceId |Y | |当該リソースを一意に識別するリソース ID URI|
|resourceRegion |Y | |影響を受けるリソースのリージョン/場所|
|portalLink |Y | |リソースの概要ページへの Azure ポータルの直接のリンク|
|プロパティ |N |省略可能 |イベントの詳細を含む<Key  Value>ペア (つまり、ディクショナリ<String  String>) のセット。properties フィールドは省略可能です。カスタム UI またはロジック アプリ ベースのワークフローでは、ユーザーは、ペイロードを使用して渡すことのできるキーと値を入力できます。Webhook URI 自体を (クエリ パラメーターとして) 使用して、カスタム プロパティを Webhook に戻すこともできます。|


>[AZURE.NOTE]ポータルで properties フィールドを使用することはできません。Insights SDK の今後のリリースでは、アラート API を使用して properties を設定できます。

## 次のステップ

詳しい背景を知るために、[Azure アラートと PagerDuty との統合](http://go.microsoft.com/fwlink/?LinkId=627080)に関するビデオで Azure アラートと Webhook についてさらに学習します。

プログラムで Webhook を作成する方法を学習する場合は、[Azure Insights SDK を使用する Webhook でのアラートの作成 (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a) に関するページを参照してください。

Webhook とアラートを設定したら、他のオプションを調べ、自動化スクリプトを開始します。

[Azure Automation スクリプト (Runbook) の実行](http://go.microsoft.com/fwlink/?LinkId=627081)

Azure アラートを使用して、メッセージを他のサービスに送信します。次の例のテンプレートを使用して、開始します。

[Twilio API 経由で SMS を送信するロジック アプリを使用する場合](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Slack メッセージを送信するロジック アプリを使用する場合](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Azure キューにメッセージを送信するロジック アプリを使用する場合](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=Oct15_HO3-->