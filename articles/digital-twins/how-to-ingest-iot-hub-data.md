---
title: IoT Hub からテレメトリを取り込む
titleSuffix: Azure Digital Twins
description: IoT Hub からデバイスのテレメトリ メッセージを取り込む方法について説明します。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c73f007f85a963a09de4e05222082fd52f784c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131567"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins に IoT Hub テレメトリを取り込む

Azure Digital Twins は、IoT デバイスやその他のソースからのデータに基づいています。 Azure Digital Twins で使用するデバイス データの一般的なソースは [IoT Hub](../iot-hub/about-iot-hub.md) です。

プレビュー段階で、Azure Digital Twins にデータを取り込むプロセスとは、外部コンピューティング リソースを設定することです。たとえば、データを受け取り、[DigitalTwins API](how-to-use-apis-sdks.md) を使用してプロパティを設定したり、それに応じて[デジタル ツイン](concepts-twins-graph.md)に対してテレメトリ イベントを発生させたりする [Azure 関数](../azure-functions/functions-overview.md)などです。 

このハウツー ドキュメントでは、IoT Hub からテレメトリを取り込むことができる Azure 関数を作成するプロセスについて手順を追って説明します。

## <a name="example-telemetry-scenario"></a>テレメトリのシナリオ例

このハウツーでは、Azure 関数を使用して、IoT Hub から Azure Digital Twins にメッセージを送信する方法の概要について説明します。 このために使用できる構成とマッチング戦略は多数ありますが、この記事の例には次のパーツが含まれています。
* 既知のデバイス ID を持つ IoT Hub の温度計デバイス。
* ID が一致するデバイスを表すデジタル ツイン
* 部屋を表すデジタル ツイン

> [!NOTE]
> この例では、デバイス ID と対応するデジタル ツインの ID の間の単純な ID 一致を使用していますが、(マッピング テーブルなどを使用して) デバイスからツインへのより高度なマッピングを用意することもできます。

温度計デバイスから温度テレメトリ イベントが送信されるたびに、*Room* ツインの *temperature* プロパティが更新されます。 これを実現するには、デバイスのテレメトリ イベントからデジタル ツインのプロパティ セッターにマップします。 [ツイン グラフ](concepts-twins-graph.md)のトポロジ情報を使用して *Room* ツインを見つけます。これでツインのプロパティを設定できます。 他のシナリオでは、ユーザーが一致するツイン (この例では、ID が *123* のツイン) にプロパティを設定する場合があります。 Azure Digital Twins を使用すると、テレメトリ データをツインにマップする方法を柔軟に決定できます。 

このシナリオの概要を次の図に示します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="IoT Hub デバイスからは、IoT Hub、Event Grid、またはシステム トピックを介して Azure テレメトリが Azure 関数に送信され、Azure Digital Twins でツインの temperature プロパティが更新されます。" border="false":::

## <a name="prerequisites"></a>前提条件

この例を続行する前に、次の前提条件を満たす必要があります。
1. IoT Hub を作成します。 手順については、[この IoT Hub のクイックスタート](../iot-hub/quickstart-send-telemetry-cli.md)の「*IoT Hub の作成*」セクションを参照してください。
2. IoT Hub からのイベントを処理する Azure 関数を少なくとも 1 つ作成します。 「[*データを処理するため Azure 関数を設定する*](how-to-create-azure-function.md)」方法を参照して、Azure Digital Twins に接続して Azure Digital Twins API 関数を呼び出すことができる基本的な Azure 関数を構築します。 このハウツーの残りの部分は、この関数に基づいています。
3. ハブ データのイベントの宛先を設定します。 [Azure portal](https://portal.azure.com/) で、IoT Hub インスタンスに移動します。 *[イベント]* で、Azure 関数のサブスクリプションを作成します。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure portal: イベント サブスクリプションの追加":::

4. *[イベント サブスクリプションの作成]* ページで、各フィールドに次のように入力します。
   * *[イベント サブスクリプションの詳細]* で、サブスクリプションに任意の名前を付けます
   * *[イベントの種類]* で、フィルター処理するイベントの種類として *[Device Telemetry]\(デバイス テレメトリ\)* を選択します
      - 必要に応じて、他のイベントの種類にフィルターを追加します。
   * *[エンドポイントの詳細]* で、Azure 関数をエンドポイントとして選択します

## <a name="create-an-azure-function-in-visual-studio"></a>Visual Studio で Azure 関数を作成する

このセクションでは、同じ Visual Studio のスタートアップ手順と、Azure 関数スケルトンを使用します。これについては、[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。 このスケルトンを使用すると、認証を処理し、サービス クライアントを作成することができます。これにより、データを処理し、応答として Azure Digital Twins API を呼び出すことができるようになります。 

スケルトン関数の中核は次のとおりです。

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

次の手順では、IoT Hub からの IoT テレメトリ イベントを処理するための特定のコードを追加します。  

## <a name="add-telemetry-processing"></a>テレメトリの処理を追加する

テレメトリ イベントは、デバイスからのメッセージの形式で送信されます。 テレメトリ処理コードを追加する最初の手順は、このデバイス メッセージの関連部分を Event Grid イベントから抽出することです。 

デバイスによってメッセージの構成が異なる場合があるため、この手順のコードは接続されているデバイスによって変わります。 

次のコードは、テレメトリを JSON として送信する単純なデバイスの例を示しています。 このサンプルでは、メッセージを送信したデバイスのデバイス ID と温度値を抽出します。

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

この演習の目的は、ツイン グラフ内の *Room* の温度の更新であることを思い出してください。 つまり、メッセージのターゲットは、このデバイスに関連付けられているデジタル ツインではなく、その親である *Room* ツインです。 上記のコードを使用して、テレメトリ メッセージから抽出したデバイス ID 値を使用し、親ツインを見つけることができます。

これを行うには、Azure Digital Twins API を使用して、デバイスを表すツイン (この場合、デバイスと同じ ID を持つもの) への受信リレーションシップにアクセスします。 受信リレーションシップから、次のコード スニペットを使用して親の ID を見つけることができます。

次のコード スニペットは、ツインの受信リレーションシップを取得する方法を示しています。

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

ツインの親は、リレーションシップの *SourceId* プロパティにあります。

デバイスを表すツインのモデルが、1 つの受信リレーションシップのみを持つことはごく一般的です。 この場合、返される最初の (そして唯一の) リレーションシップを選択することができます。 モデルでこのツインへの複数の種類のリレーションシップが許可されている場合、複数の受信リレーションシップから選択するために、さらに指定が必要になる場合があります。 これを行う一般的な方法は、`RelationshipName` でリレーションシップを選択することです。 

*Room* を表す親ツインの ID を取得したら、そのツインに "パッチを適用する" (更新する) ことができます。 これを行うには、次のコードを使用します。

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>完全な Azure 関数コード

前のサンプルのコードを使用した、コンテキスト内の Azure 関数全体を次に示します。

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

受信リレーションシップを見つけるユーティリティ関数:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

そして、ツインにパッチを適用するユーティリティ関数:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

これで、IoT Hub からシナリオ データを読み取って解釈する Azure 関数が完成しました。

## <a name="debug-azure-function-apps-locally"></a>Azure 関数アプリをローカルでデバッグする

Event Grid トリガーを使用してローカルで Azure 関数をデバッグすることができます。 この詳細については、「[*イベント グリッド トリガーをローカルにデバッグ*](../azure-functions/functions-debug-event-grid-trigger-local.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins を使用したデータのイングレスとエグレスについて確認します。
* "[*概念: 他のサービスとの統合*](concepts-integration.md)
