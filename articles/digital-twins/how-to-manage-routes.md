---
title: エンドポイントとルートを管理する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 923ae652872246916b2a4c5e8be95871983dbe95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559828"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Azure Digital Twins でのエンドポイントとルートの管理

Azure Digital Twins では、[イベント通知](how-to-interpret-event-data.md)をダウンストリーム サービスにルーティングしたり、コンピューティング リソースに接続したりできます。 これを行うには、まず、イベントを受信できる**エンドポイント**を設定し、次に、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する[**イベント ルート**](concepts-route-events.md)を設定します。

サポートされているエンドポイントの種類は次のとおりです。
* [イベント ハブ](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントの詳細については、[Azure メッセージング サービスの選択](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)に関する記事をご覧ください。

エンドポイントとルートは、[**EventRoutes API**](how-to-use-apis-sdks.md)、[.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用して管理します。 また、[Azure portal](https://portal.azure.com) を使用して管理することもできます。

> [!NOTE]
> Azure portal を使用したイベント ルートの管理は、現在、企業ドメイン アカウントの Azure ユーザーのみが利用できます。 
>
>@outlook.com アカウントなど、個人用の [Microsoft アカウント (MSA)](https://account.microsoft.com/account/Account) を使用している場合は、Azure Digital Twins の API または CLI を使用して、この記事の説明に従ってイベント ルートを管理してください。

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントに使用するイベント ハブ、イベント グリッド トピック、または Service Bus が既に存在している必要があります。 

次の例は、Azure CLI を使用してイベント グリッド トピックを作成する方法を示しています。

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Azure CLI のコマンドに渡すことのできる Azure リージョン名のリストを出力するには、次のコマンドを実行します。
> ```azurecli
> az account list-locations -o table
> ```

トピックを作成したら、次のコマンドを使用して Azure Digital Twins にリンクできます。

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

これにより、`--endpoint-name` 引数で指定した名前で、イベント グリッド トピックを Azure Digital Twins 内で使用できるようになります。 通常は、次のセクションで Azure Digital Twins サービス API を使用して作成する、**イベント ルート**のターゲットとしてこの名前を使用します。

イベント ハブおよび Service Bus エンドポイント用の同等のコマンドがあります。

* Service Bus トピック エンドポイントを追加する (事前に作成された Service Bus リソースが必要)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* イベント ハブ エンドポイントを追加する (事前に作成されたイベント ハブ リソースが必要)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>API を使用したイベント ルートの管理

Azure Digital Twins からエンドポイントに実際にデータを送信するには、イベント ルートを定義する必要があります。 Azure Digital Twins の **EventRoutes API** を使用すると、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 イベント ルートの詳細については、[Azure Digital Twins イベントのルーティング](concepts-route-events.md)の概念に関する記事をご覧ください。

この記事のサンプルでは C# SDK を使用しています。

イベント ルートは、データ プレーン API を使用して定義します。 ルート定義にはこれらの要素を含めることができます。
* 使用するルート ID
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター 

ルート ID がない場合、メッセージは Azure Digital Twins の外部にルーティングされません。 ルート ID があり、フィルターが `true` の場合は、すべてのメッセージがエンドポイントにルーティングされます。 ルート ID があり、別のフィルターを追加した場合は、そのフィルターに基づいてメッセージがフィルター処理されます。

1 つのルートで、複数の通知とイベントの種類を選択できるようにする必要があります。 

`CreateEventRoute` は、イベント ルートを追加するために使用される SDK 呼び出しです。 その使用例を次に示します。

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

### <a name="event-route-sample-code"></a>イベント ルートのサンプル コード

次のコード サンプルは、イベント ルートを作成、一覧表示、削除する方法を示しています。
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>イベントのフィルター処理

フィルター処理を行わない場合、エンドポイントは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント
* Azure Digital Twins インスタンスで構成された[モデル](concepts-models.md)が追加または削除されたときに発生するモデル変更イベント

エンドポイントにフィルターを追加することで、送信されるイベントを制限できます。

フィルターを追加するには、*https://{YourHost}/EventRoutes/myNewRoute?api-version=2020-05-31-preview* に対して、次の本文を含む PUT 要求を使用します。

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

サポートされているルート フィルターを次に示します。

| フィルター名 | 説明 | フィルター スキーマ | サポートされている値 | 
| --- | --- | --- | --- |
| Type | デジタル ツイン インスタンスを通過する[イベントの種類](./concepts-route-events.md#types-of-event-messages) | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| source | Azure Digital Twins インスタンスの名前 | `"filter" : "source = '<hostname>'"`|  **通知の場合**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **テレメトリの場合**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| サブジェクト | 上記のイベント ソースのコンテキストでのイベントの説明 | `"filter": " subject = '<subject>'"` | **通知の場合**: サブジェクトは、サブジェクトの `<twinid>` <br> または URI 形式です。これは、次のように複数の部分または ID で一意に識別されます。<br>`<twinid>/relationships/<relationshipid>`<br> **テレメトリの場合**: ツイン コンポーネントからテレメトリが生成された場合、サブジェクトはコンポーネント パス (例: `comp1.comp2`) です。 コンポーネントからテレメトリが生成されていない場合、サブジェクト フィールドは空になります。 |
| データ スキーマ | DTDL モデル ID | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **テレメトリの場合**: データ スキーマは、テレメトリを生成するツインまたはコンポーネントのモデル ID です。 <br>**通知の場合**: データ スキーマはサポートされていません。|
| Content type | データ値のコンテンツ タイプ | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| 仕様バージョン | 使用しているイベント スキーマのバージョン | `"filter": "specversion = '<version>'"` | `1.0`である必要があります。 これは、CloudEvents スキーマ バージョン 1.0 を示しています。 |
| True/False | フィルター処理なしでルートを作成するか、ルートを無効にすることができます。 | `"filter" : "<true/false>"` | `true` = ルートはフィルター処理なしで有効になります。 <br> `false` = ルートは無効になります。 |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

次の操作を使用してフィルターを組み合わせることもできます。

| フィルター名 | フィルター スキーマ | 例 | 
| --- | --- | --- |
| AND/OR | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| AND/OR | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| 入れ子になった操作 | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> プレビュー期間中は、文字列の等価性 (=、!=) のみがサポートされます。

フィルターを実装または更新したときに、変更がデータ パイプラインに反映されるまでに数分かかることがあります。

## <a name="manage-endpoints-and-routes-with-cli"></a>CLI を使用したエンドポイントとルートの管理

エンドポイントとルートは、Azure Digital Twins CLI を使用して管理することもできます。 コマンドについては、[Azure Digital Twins CLI の使用方法](how-to-use-cli.md)に関する記事をご覧ください。

## <a name="monitor-event-routes"></a>イベント ルートの監視

カウント、待ち時間、失敗率などのルーティング メトリックは、[Azure portal](https://portal.azure.com/) で確認できます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 Azure Digital Twins インスタンスのメニューから **[メトリック]** オプションを選択して、 *[メトリック]* ページを表示します。

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Azure portal の Azure Digital Twins インスタンスの [メトリック] ページ":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [方法: イベント データを解釈する](how-to-interpret-event-data.md)
