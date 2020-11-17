---
title: リレーションシップを使ってツイン グラフを管理する
titleSuffix: Azure Digital Twins
description: リレーションシップを使って接続することでデジタル ツインのグラフを管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 78e0bfb0af494ecae2865fcc42679b8fcce44916
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359580"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>リレーションシップを使ってデジタル ツインのグラフを管理する

Azure Digital Twins の中核は、環境全体を表す[ツイン グラフ](concepts-twins-graph.md)です。 ツイン グラフは、**リレーションシップ** を介して接続された個々のデジタル ツインで構成されています。 

機能する [Azure Digital Twins インスタンス](how-to-set-up-instance-portal.md)があり、クライアント アプリで [認証](how-to-authenticate-client.md)コードを設定すると、[**DigitalTwins API**](/rest/api/digital-twins/dataplane/twins) を使用して Azure Digital Twins インスタンス内のデジタル ツインとそのリレーションシップを作成、変更、削除することができます。 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用することもできます。

この記事では、リレーションシップとグラフ全体の管理に焦点を当てます。個々のデジタル ツインの操作については、[ *「デジタル ツインを管理する」方法*](how-to-manage-twin.md)を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>リレーションシップの作成

リレーションシップには、さまざまなデジタル ツインが相互にどのように接続されるかが記述され、それによってツイン グラフの基礎が形成されます。

リレーションシップは、`CreateOrReplaceRelationshipAsync()` 呼び出しを使用して作成されます。 

リレーションシップを作成するには、以下を指定する必要があります。
* ソース ツイン ID (以下のコード サンプルの `srcId`):リレーションシップが発生したツインの ID。
* ターゲット ツイン ID (以下のコード サンプルの `targetId`):リレーションシップの対象のツインの ID。
* リレーションシップ名 (以下のコード サンプルの `relName`):_contains_ のような一般的な種類のリレーションシップ。
* リレーションシップ ID (以下のコード サンプルの `relId`):このリレーションシップの具体的な名前 (_Relationship1_ など)。

リレーションシップ ID は、指定されたソース ツイン内で一意である必要があります。 グローバルに一意である必要はありません。
たとえば、ツイン *foo* の場合、それぞれのリレーションシップ ID は一意である必要があります。 ただし、別のツインである *bar* は、*foo* リレーションシップの同じ ID と一致する発信リレーションシップを持つことができます。

次のコード サンプルは、Azure Digital Twins インスタンスにリレーションシップを作成する方法を示しています。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
main メソッドでは、`CreateRelationship()` 関数を呼び出して、次のような _contains_ リレーションシップを作成できます。 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
複数のリレーションシップを作成する場合は、同じメソッドの呼び出しを繰り返して、異なるリレーションシップの種類を引数に渡すことができます。 

ヘルパー クラス `BasicRelationship` の詳細については、[ *「Azure Digital Twins の API および SDK を使用する」方法*](how-to-use-apis-sdks.md#serialization-helpers)を参照してください。

### <a name="create-multiple-relationships-between-twins"></a>ツイン間でリレーションシップの作成

リレーションシップは次のいずれかに分類できます。 

* 発信リレーションシップ:他のツインに接続するために外側を指す、このツインに属するリレーションシップ。 `GetRelationshipsAsync()` メソッドは、ツインの発信リレーションシップを取得するために使用されます。
* 受信リレーションシップ:"受信" リンクを作成するためにこのツインを指す、他のツインに属するリレーションシップ。 `GetIncomingRelationshipsAsync()` メソッドは、ツインの受信リレーションシップを取得するために使用されます。

2 つのツイン間のリレーションシップの数には制限がありません。ツイン間には、必要な数のリレーションシップを含めることができます。 

これは、2 つのツイン間に、さまざまな種類のリレーションシップを同時に複数表すことができることを意味します。 たとえば、"*ツイン A*" と "*ツイン B*" の間に、"*格納された*" リレーションシップと "*製造された*" リレーションシップを含めることができます。

必要に応じて、同じ 2 つのツイン間に、同じ種類のリレーションシップのインスタンスを複数作成することもできます。 この例では、リレーションシップのリレーションシップ ID が異なる限り、"*ツイン A*" は "*ツイン B*" と 2 つの異なる "*格納された*" リレーションシップを持つことができます。

## <a name="list-relationships"></a>リレーションシップの一覧表示

グラフ内の特定のツインの "**発信**" リレーションシップの一覧にアクセスするには、次のように `GetRelationships()` メソッドを使用できます。

```csharp
await client.GetRelationships()
```

これによって、呼び出しの同期または非同期のバージョンのどちらを使用するかに応じて、`Azure.Pageable<T>` または `Azure.AsyncPageable<T>` が返されます。

リレーションシップの一覧を取得する例を次に示します。

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
これで、このメソッドを呼び出して、次のようなツインの発信リレーションシップを確認できます。

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
取得したリレーションシップを使用して、グラフ内の他のツインに移動できます。 これを行うには、返されたリレーションシップから `target` フィールドを読み取り、それを `GetDigitalTwin()` への次の呼び出しの ID として使用します。

### <a name="find-incoming-relationships-to-a-digital-twin"></a>デジタル ツインとの受信リレーションシップを見つける

Azure Digital Twins には、特定のツインとのすべての "_ *受信**" リレーションシップを検索するための API もあります。 これは、逆方向のナビゲーションの場合やツインを削除するときに便利です。

前のコード サンプルは、ツインからの発信リレーションシップの検索に焦点を合わせていました。 次の例は同じような構造になってますが、代わりにツインへの "*受信*" リレーションシップを検索します。

`IncomingRelationship` の呼び出しからは、リレーションシップ全体が返されないことに注意してください。

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

これで、このメソッドを呼び出して、次のようなツインの受信リレーションシップを確認できます。

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>ツインのすべてのプロパティとリレーションシップを一覧表示する

ツインへの発信と受信のリレーションシップを一覧表示する上記のメソッドを使用すると、ツインのプロパティとその両方の種類のリレーションシップを含む完全なツインの情報を出力するメソッドを作成できます。 この実行方法を示す、`FetchAndPrintTwinAsync()` というメソッドの例を次に示します。

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

これで、次のように main メソッドでこの関数を呼び出すことができるようになります。 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>リレーションシップの削除

最初のパラメーターには、ソース ツイン (リレーションシップの発生元のツイン) を指定します。 もう一方のパラメーターはリレーションシップ ID です。 リレーションシップ ID はツインのスコープ内でのみ一意であるため、ツイン ID とリレーションシップ ID の両方が必要です。

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

これで、このメソッドを呼び出して、次のようなリレーションシップを削除できるようになります。

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>ツイン グラフを作成する 

次の実行可能なコード スニペットでは、この記事のリレーションシップ操作を使用して、デジタル ツインとリレーションシップからツイン グラフを作成します。

### <a name="set-up-the-runnable-sample"></a>実行可能なサンプルを設定する

このスニペットでは、[*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) と [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) モデル定義を使用しています。「[*チュートリアル:サンプル クライアント アプリを使用して Azure Digital Twins を試す*](tutorial-command-line-app.md)」のものです。 これらのリンクを使用してファイルに直接移動するか、[こちら](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)の完全なエンドツーエンドのサンプル プロジェクトの一部としてダウンロードすることができます。 

このサンプルを実行する前に、以下を実行します。
1. モデル ファイルをダウンロードしてプロジェクトに配置し、以下のコードの `<path-to>` プレースホルダーを置き換えて、プログラムに検索場所を指示します。
2. プレースホルダー `<your-instance-hostname>` を Azure Digital Twins インスタンスのホスト名に置き換えます。
3. 次のパッケージをプロジェクトに追加します。
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

サンプルを直接実行する場合は、ローカルの資格情報も設定する必要があります。 次のセクションでは、これについて説明します。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>サンプルを実行する

上記の手順を完了すると、次のサンプル コードを直接実行できます。

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

上記のプログラムのコンソール出力は次のようになります。 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="ツインの詳細、ツインの受信および発信リレーションシップが表示されているコンソール出力。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> ツイン グラフは、ツイン間にリレーションシップを作成する概念です。 ツイン グラフの視覚的表現を表示する場合は、この記事の [_視覚化*](how-to-manage-graph.md#visualization)に関するセクションを参照してください。 

### <a name="create-a-twin-graph-from-a-csv-file"></a>CSV ファイルからツイン グラフを作成する

実際の使用例では、多くの場合、別のデータベース、または場合によってはスプレッドシートまたは CSV ファイルに格納されたデータからツイン階層が作成されます。 このセクションでは、CSV ファイルからデータを読み取り、そこからツイン グラフを作成する方法について説明します。

デジタル ツインのセットとリレーションシップが説明されている次のデータ テーブルがあるとします。

|  モデル ID    | ツイン ID (一意である必要があります) | [リレーションシップ名]  | ターゲット ツイン ID  | ツインの init データ |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | contains | Room1 | |
| dtmi:example:Floor;1    | Floor0 | contains | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature":80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature":70} |

このデータを Azure Digital Twins に取り込む方法の 1 つとして、テーブルを CSV ファイルに変換してコードを記述し、ファイルをコマンドに解釈して、ツインやリレーションシップを作成するという方法があります。 次のコード サンプルは、CSV ファイルからのデータの読み取り、および Azure Digital Twins でのツイン グラフの作成を示しています。

次のコードでは、CSV ファイルは *data.csv* と呼ばれ、Azure Digital Twins インスタンスの **hostname** を表すプレースホルダーがあります。 また、このサンプルでは、プロジェクトに追加できるパッケージをいくつか使用すると、このプロセスを実行しやすくなります。

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
    }
}

```
## <a name="manage-relationships-with-cli"></a>CLI を使用してリレーションシップを管理する

ツインとそのリレーションシップは、Azure Digital Twins CLI を使用して管理することもできます。 コマンドについては、[ *「Azure Digital Twins CLI を使用する」方法*](how-to-use-cli.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins ツイン グラフのクエリについて確認します。
* "[*概念: クエリ言語*](concepts-query-language.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)