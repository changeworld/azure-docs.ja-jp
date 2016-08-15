<properties 
	pageTitle="Azure App Service でロジック アプリを監視する | Microsoft Azure" 
	description="ロジック アプリの作業内容を表示する方法" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# ロジック アプリを監視する

[ロジック アプリを作成](app-service-logic-create-a-logic-app.md)したら、Azure ポータルでそのすべての実行履歴を表示できます。また、Azure 診断や Azure アラートなどのサービスを設定することで、リアルタイムにイベントを監視し、"1 時間あたりの失敗の回数が 5 回を超えたとき" など、さまざまなイベントに応じてアラートを発生させることもできます。

## Azure ポータルでの監視

履歴を表示するには、**[参照]** を選択し、**[Logic Apps]** を選択します。サブスクリプション内のすべての Logic Apps の一覧が表示されます。監視するロジック アプリを選択します。このロジック アプリで発生したすべてのアクションとトリガーが一覧表示されます。

![概要](./media/app-service-logic-monitor-your-logic-apps/overview.png)

このブレードには、重要な情報を表示するセクションがいくつか存在します。

- **[概要]** には、**[すべての実行]** と **[トリガーの履歴]** が一覧表示されます。
	- **[すべての実行]** には、ロジック アプリの直近の実行履歴が一覧表示されます。いずれかの行をクリックすると、その実行に関する詳細な情報が表示されます。また、タイルをクリックすると、さらにさかのぼって実行履歴が一覧表示されます。
	- **[トリガーの履歴]** には、このロジック アプリのすべてのトリガー アクティビティが一覧表示されます。トリガー アクティビティのステータスには、新しいデータのチェック (新しいファイルが FTP に追加されたかどうかの監視など) がスキップされたことを意味する "スキップ済み" と、データが返されてロジック アプリが開始されたことを意味する "成功"、構成に誤りがあることを示す "失敗" があります。
- **[診断]** では、実行時の詳細情報やイベントを表示したり、[Azure アラート](#adding-azure-alerts)をサブスクライブしたりすることができます。

### 実行の詳細の表示

この実行リストには、特定の実行の**状態**、**開始時刻**、**期間**が表示されます。実行の詳細を表示する行を選択します。

監視ビューには、実行の各ステップ、入力と出力、エラー メッセージ (発生した場合) が表示されます。

![実行とアクション](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

REST API で必要となる実行の**関連付け ID** など、さらに詳しい情報が必要な場合は、**[実行の詳細]** ボタンをクリックしてください。その表示内容には、実行のすべてのステップ、状態、入力/出力が含まれます。

## Azure 診断とアラート

これまでに挙げた Azure ポータルと REST API から得られる情報に加え、Azure 診断を使って、さらに踏み込んだ情報の入手やデバッグを行うようにロジック アプリを構成することができます。

1. ロジック アプリ ブレードの **[診断]** セクションをクリックします。
1. **[診断設定]** の構成をクリックします。
1. データの出力先となるイベント ハブまたはストレージ アカウントを構成します。

	![Azure Diagnostics settings](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### Azure アラートの追加

診断の構成を終えたら、特定のしきい値を超えたときに発生する Azure アラートを追加することができます。**[診断]** ブレードの **[アラート]** タイルを選択し、**[アラートの追加]** を選択します。さまざまなしきい値とメトリックに基づくアラートを画面の指示に従って構成することができます。

![Azure Alert Metrics](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

**[条件]**、**[しきい値]**、**[期間]** を適宜構成してください。最後に、通知の送信先となる電子メール アドレスや Webhook を構成することができます。ロジック アプリから[要求トリガー](../connectors/connectors-native-reqres.md)を使用してアラートに基づく動作を実現することもできます ([Slack へのポスト](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)、[テキスト送信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)、[キューへのメッセージの追加](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)など)。

### Azure 診断の設定

これらのイベントにはそれぞれ、ロジック アプリやイベントに関する詳しい情報 (状態など) が含まれています。次に示したのは、*ActionCompleted* イベントの例です。

```javascript
{
			"time": "2016-07-09T17:09:54.4773148Z",
			"workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
			"resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
			"category": "WorkflowRuntime",
			"level": "Information",
			"operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
			"properties": {
				"$schema": "2016-06-01",
				"startTime": "2016-07-09T17:09:53.4336305Z",
				"endTime": "2016-07-09T17:09:53.5430281Z",
				"status": "Succeeded",
				"code": "OK",
				"resource": {
					"subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
					"resourceGroupName": "MyResourceGroup",
					"workflowId": "cff00d5458f944d5a766f2f9ad142553",
					"workflowName": "MyLogicApp",
					"runId": "08587361146922712057",
					"location": "eastus",
					"actionName": "Http"
				},
				"correlation": {
					"actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
					"clientTrackingId": "my-custom-tracking-id"
				},
				"trackedProperties": {
					"myProperty": "<value>"
				}
			}
		}
```

追跡と監視で特に重要となるプロパティは、*clientTrackingId* と *trackedProperties* の 2 つです。

#### クライアント追跡 ID

クライアント追跡 ID は、ロジック アプリ (ロジック アプリの構成要素として呼び出された入れ子になったワークフローを含む) の実行中に発生したイベントを相互に関連付ける値です。クライアント追跡 ID は、指定されていなければ自動的に生成されますが、トリガー要求 (要求トリガー、HTTP トリガー、Webhook トリガーのいずれか) で、ID 値を含んだ `x-ms-client-tracking-id` ヘッダーを渡すことによってトリガーから手動で指定することもできます。

#### 追跡対象のプロパティ

ワークフロー定義で追跡対象のプロパティをアクションに追加すると、診断データで入力または出力を追跡することができます。これは、"オーダー ID" などテレメトリ内のデータを追跡する場合に有効活用できます。追跡対象のプロパティを追加するには、アクションの `trackedProperties` プロパティを追加します。追跡対象のプロパティで追跡できるのは、1 つのアクションの入力と出力だけです。ただし、イベントの `correlation` プロパティを使用することで、実行内の複数のアクションにわたってそれらを相互に関連付けることができます。

```javascript
{
	"myAction": {
		"type": "http",
		"inputs": {
			"uri": "http://uri",
			"headers": {
				"Content-Type": "application/json"
			},
			"body": "@triggerBody()"
		},
		"trackedProperties":{
			"myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
			"myActionHTTPValue": "@action()['outputs']['body']['foo']",
			"transactionId": "@action()['inputs']['body']['bar']"
		}
	}
}
```

### ソリューションの拡張

このテレメトリをイベント ハブやストレージから他のサービス ([Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)、[Power BI](https://powerbi.com) など) に渡すことで、統合ワークフローをリアルタイムに監視することができます。

## 次のステップ
- [ロジック アプリの接続の例とシナリオ](app-service-logic-examples-and-scenarios.md)
- [ロジック アプリ デプロイ テンプレートの作成](app-service-logic-create-deploy-template.md)
- [エンタープライズ統合機能](app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0803_2016-->