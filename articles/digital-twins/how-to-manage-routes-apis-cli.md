---
title: エンドポイントとルートを管理する (API と CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7016abc9d52aa12b497d29f605fe351ee3f6a2dd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519115"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins のエンドポイントとルートを管理する (API と CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](how-to-interpret-event-data.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる **エンドポイント** を設定します。 そのうえで、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する [**イベント ルート**](concepts-route-events.md)を作成します。

この記事では、[Event Routes API](/rest/api/digital-twins/dataplane/eventroutes)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、および [Azure Digital Twins CLI](how-to-use-cli.md) を使用して、エンドポイントとルートを作成する手順について説明します。

別の方法として、エンドポイントとルートは、[Azure portal](https://portal.azure.com) で管理することもできます。 ポータルを使用したバージョンの記事については、[*エンドポイントとルートをポータルで管理する方法*](how-to-manage-routes-portal.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure アカウント** が必要となります ([こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます)。
* ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス** が必要となります。 まだインスタンスをお持ちでない場合は、インスタンスを作成してください。その手順については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-cli.md)に関するページを参照してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントのタイプの詳細については、"[*Azure メッセージング サービスの選択*](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントに使用する Event Grid トピック、イベント ハブ、または Service Bus が既に存在している必要があります。 

### <a name="create-an-event-grid-endpoint"></a>Event Grid のエンドポイントを作成する

次の例は、Azure CLI を使用して Event Grid タイプのエンドポイントを作成する方法を示しています。 [Azure Cloud Shell](https://shell.azure.com) を使用するか、[CLI をローカルにインストール](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)してください。

まず、Event Grid トピックを作成します。 次のコマンドを使用することができます。または、Event Grid の "*カスタム イベント*" に関するクイックスタートの [「*カスタム トピックの作成*」セクション](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)にアクセスして、詳しい手順を参照してください。

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Azure CLI のコマンドに渡すことのできる Azure リージョン名のリストを出力するには、次のコマンドを実行します。
> ```azurecli-interactive
> az account list-locations -o table
> ```

トピックを作成したら、次の [Azure Digital Twins CLI コマンド](how-to-use-cli.md)を使用して Azure Digital Twins にリンクできます。

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

これにより、`--endpoint-name` 引数に指定した名前で、Event Grid トピックを Azure Digital Twins 内のエンドポイントとして使用できるようになりました。 通常は、**イベント ルート** のターゲットとしてこの名前を使用します。これは、[この記事の中で後から](#create-an-event-route) Azure Digital Twins サービス API を使用して作成します。

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Event Hubs または Service Bus のエンドポイントを作成する

Event Hubs または Service Bus のエンドポイントを作成する手順は、前述した Event Grid のエンドポイントを作成する手順と似ています。

まず、エンドポイントとして使用するリソースを作成します。 必要なものは以下のとおりです。
* Service Bus:"_Service Bus 名前空間_"、"_Service Bus トピック_"、"_承認規則_"
* Event Hubs:_Event Hubs 名前空間_、_イベント ハブ_、_承認規則_

さらに、次のコマンドを使用して、Azure Digital Twins にエンドポイントを作成します。 

* Service Bus トピック エンドポイントを追加する (事前に作成された Service Bus リソースが必要)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs エンドポイントを追加する (事前に作成された Event Hubs リソースが必要)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>配信不能処理を行うエンドポイントを作成する

エンドポイントでは、一定期間内にイベントを配信できない場合や、イベントの配信を一定回数試行した後も配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。

配信不能処理の詳細については、[*イベント ルートの概念*](concepts-route-events.md#dead-letter-events)に関するページを参照してください。 配信不能処理を構成してエンドポイントを設定する方法については、このセクションの残りの部分を読み進めます。

#### <a name="set-up-storage-resources"></a>ストレージ リソースを設定する

配信不能の場所を設定するには、ご使用の Azure アカウントに[コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)が設定された[ストレージ アカウント](../storage/common/storage-account-create.md?tabs=azure-portal)が必要です。 

後でエンドポイントを作成する際に、このコンテナーの URL を指定します。 配信不能の場所は、[SAS トークン](../storage/common/storage-sas-overview.md)を含むコンテナー URL としてエンドポイントに提供されます。 このトークンには、ストレージ アカウント内の宛先コンテナーに対する `write` アクセス許可が必要です。 完全な形式の URL は `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>` の形式になります。

以下の手順に従って、Azure アカウントでこれらのストレージ リソースを設定し、次のセクションでエンドポイント接続の設定を準備します。

1. 「[*ストレージ アカウントを作成する*](../storage/common/storage-account-create.md?tabs=azure-portal)」の手順に従って、Azure サブスクリプションに **ストレージ アカウント** を作成します。 後で使用するために、そのストレージ アカウント名を書き留めておきます。
2. 「[*コンテナーを作成する*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」の手順に従って、新しいストレージ アカウント内に **コンテナー** を作成します。 後で使用するために、そのコンテナー名を書き留めておきます。
3. 次に、ストレージ アカウント用に **SAS トークン** を作成して、エンドポイントからアクセスするときにこれを使用できるようにします。 まず、[Azure portal](https://ms.portal.azure.com/#home) でご自分のストレージ アカウントに移動します (ポータルの検索バーを使って名前で見つけることができます)。
4. ストレージ アカウントのページで、左側のナビゲーション バーの _[Shared Access Signature]_ リンクを選択し、SAS トークンの設定を開始します。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Azure portal の [ストレージ アカウント] ページ" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. *Shared Access Signature のページ* の *[使用できるサービス]* と *[使用できるリソースの種類]* で、目的の設定を選択します。 カテゴリごとに少なくとも 1 つのボックスを選択する必要があります。 *[与えられているアクセス許可]* で **[書き込み]** を選択します (必要に応じて他のアクセス許可を選択することもできます)。
1. 残りの設定には、任意の値を設定します。
1. 完了後、 _[SAS と接続文字列を生成する]_ ボタンを選択して SAS トークンを生成します。 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Azure portal の [ストレージ アカウント] ページで SAS トークンを生成するためのすべての設定の選択が示されている。[SAS と接続文字列を生成する] ボタンが強調表示されている" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. これにより、同じページの下部の選択した設定の下に、いくつかの SAS と接続文字列の値が生成されます。 下にスクロールして値を表示し、 *[クリップボードにコピー]* アイコンを使用して **SAS トークン** 値をコピーします。 後で使用するために保存します。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="配信不能メッセージのシークレットで使用する SAS トークンをコピーします。" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="configure-the-endpoint"></a>エンドポイントを構成する

配信不能処理が有効になっているエンドポイントを作成するには、Azure Resource Manager API を使用してエンドポイントを作成する必要があります。 

1. 最初に、[Azure Resource Manager API のドキュメント](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)を使用して、エンドポイントを作成するための要求を設定し、必要な要求パラメーターを入力します。 

1. 次に、要求の **本文** で `deadLetterSecret` フィールドをプロパティ オブジェクトに追加します。 この値を以下のテンプレートに従って設定します。これにより、[前のセクション](#set-up-storage-resources)で収集したストレージ アカウント名、コンテナー名、SAS トークンの値から URL が生成されます。
      
    ```json
    {
      "properties": {
        "endpointType": "EventGrid",
        "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
        "accessKey1": "xxxxxxxxxxx",
        "accessKey2": "xxxxxxxxxxx",
        "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
      }
    }
    ```
1. 要求を送信してエンドポイントを作成します。

この要求の構造化に関する詳細については、次の Azure Digital Twins REST API のドキュメントを参照してください:「[エンドポイント - DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)」。

### <a name="message-storage-schema"></a>メッセージ ストレージ スキーマ

配信不能処理を行うエンドポイントが設定されると、配信不能メッセージが次の形式でストレージ アカウントに格納されます。

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

配信不能メッセージは、元のエンドポイントに配信されることを意図していた元のイベントのスキーマと一致します。

次に、[ツイン作成通知](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)の配信不能メッセージの例を示します。

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>イベント ルートを作成する

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート** を定義する必要があります。 Azure Digital Twins の **EventRoutes API** を使用すると、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 イベント ルートの詳細については、[*Azure Digital Twins イベントのルーティングの概念*](concepts-route-events.md)に関する記事を参照してください。

このセクションのサンプルでは、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用しています。

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

> [!NOTE]
> エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる **前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートのデプロイが失敗する場合は、数分待ってからやり直してください。
>
> このフローをスクリプト化する場合は、ルートの設定に進む前に、エンドポイント サービスのデプロイが完了するまでの 2 ～ 3 分の待機時間を組み込んで、このことを考慮に入れておくことを推奨します。

### <a name="creation-code-with-apis-and-the-c-sdk"></a>API と C# SDK を使用した作成コード

イベント ルートは、[データ プレーン API](how-to-use-apis-sdks.md#overview-data-plane-apis) を使用して定義します。 

ルート定義にはこれらの要素を含めることができます。
* 使用するルート名
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター 

ルート名がない場合、メッセージは Azure Digital Twins の外部にルーティングされません。 ルート名があり、フィルターが `true` の場合は、すべてのメッセージがエンドポイントにルーティングされます。 ルート名があり、別のフィルターを追加した場合は、そのフィルターに基づいてメッセージがフィルター処理されます。

1 つのルートで、複数の通知とイベントの種類を選択できるようにする必要があります。 

`CreateOrReplaceEventRouteAsync` は、イベント ルートを追加するために使用される SDK 呼び出しです。 その使用例を次に示します。

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("<your-endpointName>", eventFilter);
await client.CreateOrReplaceEventRouteAsync("routeName", er);
```
    
> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

### <a name="event-route-sample-code"></a>イベント ルートのサンプル コード

次のサンプル メソッドは、イベント ルートを作成、一覧表示、削除する方法を示しています。
```csharp
private async static Task CreateEventRoute(DigitalTwinsClient client, String routeName, DigitalTwinsEventRoute er)
{
  try
  {
    Console.WriteLine("Create a route: testRoute1");
            
    // Make a filter that passes everything
    er.Filter = "true";
    await client.CreateOrReplaceEventRouteAsync(routeName, er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable<DigitalTwinsEventRoute> result = client.GetEventRoutes();
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
  }
    catch (RequestFailedException e)
    {
        Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
    }
  }
```

## <a name="filter-events"></a>イベントのフィルター処理

フィルター処理を行わない場合、エンドポイントは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント

イベント ルートにエンドポイントの **フィルター** を追加することで、送信されるイベントを制限できます。

フィルターを追加するために、次の本文を含む PUT 要求を *https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31* に対して使用できます。

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 
サポートされているルート フィルターを次に示します。 「*フィルター テキスト スキーマ*」列の情報を使用して、上記の要求本文の `<filter-text>` プレースホルダーを置き換えてください。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>CLI を使用したエンドポイントとルートの管理

エンドポイントとルートは、Azure Digital Twins CLI を使用して管理することもできます。 CLI の使用および利用できるコマンドについて詳しくは、"[*Azure Digital Twins CLI を使用する方法*](how-to-use-cli.md)" に関するページを参照してください。

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [*方法: イベント データを解釈する*](how-to-interpret-event-data.md)
