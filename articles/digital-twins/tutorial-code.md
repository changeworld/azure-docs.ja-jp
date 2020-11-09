---
title: チュートリアル:クライアント アプリをコーディングする
titleSuffix: Azure Digital Twins
description: .NET (C#) SDK を使用してクライアント アプリの最小限のコードを記述するチュートリアル。
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 3225fff1c82822dee990804f934ada86068841e8
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280269"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>チュートリアル:Azure Digital Twins API を使用したコーディング

Azure Digital Twins を使用する開発者は、Azure Digital Twins サービスのインスタンスとのやり取りのために、クライアント アプリケーションを作成するのが一般的です。 この開発者向けのチュートリアルでは、[.NET 用 Azure Digital Twins SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用した、Azure Digital Twins サービスに対するプログラミングの概要を説明します。 C# コンソール クライアント アプリの作成手順を最初から順を追って説明します。

> [!div class="checklist"]
> * プロジェクトの設定
> * プロジェクト コードでの作業開始   
> * 完全なコード例
> * リソースをクリーンアップする
> * 次のステップ

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、セットアップとプロジェクトの作業にコマンド ラインを使用します。 そのため、任意のコード エディターを使用して演習を進めることができます。

開始するために必要なもの:
* 任意のコード エディター
* 開発マシンにインストールされた **.NET Core 3.1** 。 「[.NET Core 3.1 のダウンロード](https://dotnet.microsoft.com/download/dotnet-core/3.1)」から、複数のプラットフォームに対応する .NET Core SDK のこのバージョンをダウンロードできます。

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>プロジェクトの設定

Azure Digital Twins インスタンスを使用する準備ができたら、クライアント アプリ プロジェクトの設定を開始します。 

ご使用のコンピューターでコマンド プロンプトまたはその他のコンソール ウィンドウを開き、このチュートリアルでの作業を格納する空のプロジェクト ディレクトリを作成します。 ディレクトリに任意の名前を指定します (たとえば、 *DigitalTwinsCodeTutorial* )。

その新しいディレクトリに移動します。

プロジェクト ディレクトリに移動したら、 **空の .NET コンソール アプリ プロジェクトを作成** します。 コマンド ウィンドウで次のコマンドを実行して、コンソール用の最小限の C# プロジェクトを作成できます。

```cmd/sh
dotnet new console
```

これにより、コードの大部分を記述する *Program.cs* と呼ばれるものを含む、いくつかのファイルがこのディレクトリ内に作成されます。

コマンド ウィンドウは、チュートリアル全体を通して使用するため、開いたままにしておきます。

次に、Azure Digital Twins を操作するために必要な **2 つの依存関係をプロジェクトに追加** します。 以下のリンクを使用すると、NuGet のパッケージに移動できます。そこでコンソール コマンド (.NET CLI を含む) を見つけて、それぞれの最新バージョンをプロジェクトに追加できます。
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 これは、[.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) のパッケージです。 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity)。 このライブラリでは、Azure に対する認証を支援するツールが提供されます。

## <a name="get-started-with-project-code"></a>プロジェクト コードでの作業開始

このセクションでは、Azure Digital Twins とやり取りする新しいアプリ プロジェクトのコードの記述を開始します。 説明するアクションは次のとおりです。
* サービスに対する認証
* モデルのアップロード
* エラーのキャッチ
* デジタル ツインの作成
* リレーションシップの作成
* デジタル ツインのクエリ

チュートリアルの最後にコード全体を示すセクションもあります。 これを参考にして、ご自分のプログラムを確認しながら進めることができます。

最初に、任意のコード エディターで *Program.cs* ファイルを開きます。 次のような最小限のコードのテンプレートが表示されます。

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

まず、コードの先頭に `using` 行をいくつか追加して、必要な依存関係を取得します。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

次に、何らかの機能を記述するコードをこのファイルに追加します。 

### <a name="authenticate-against-the-service"></a>サービスに対する認証

アプリで最初に行う必要があるのは、Azure Digital Twins サービスに対して認証することです。 その後、SDK 関数にアクセスするためのサービス クライアント クラスを作成できます。

認証するには、Azure Digital Twins インスタンスの *hostName* が必要です。

*Program.cs* で、次のコードを "Hello, World!" の 出力行 (`Main` メソッド内) の下に貼り付けます。 `adtInstanceUrl` の値を Azure Digital Twins インスタンスの *hostName* に設定します。

```csharp
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
Console.WriteLine($"Service client created – ready to go");
```

ファイルを保存します。 

コマンド ウィンドウで、次のコマンドを使用してコードを実行します。 

```cmd/sh
dotnet run
```

これにより、最初の実行での依存関係が復元されてから、プログラムが実行されます。 
* エラーが発生しなかった場合は、プログラムで " *Service client created - ready to go* " が出力されます。
* このプロジェクトにはまだエラー処理がないため、何らかの問題が発生すると、コードによってスローされた例外が表示されます。

### <a name="upload-a-model"></a>モデルのアップロード

Azure Digital Twins には、組み込みのドメイン ボキャブラリはありません。 Azure Digital Twins で表すことができる環境内の要素の種類は、 **モデル** を使用してご自分で定義します。 [モデル](concepts-models.md)は、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、[デジタル ツイン](concepts-twins-graph.md)のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 それらは、JSON に似た **Digital Twins Definition Language (DTDL)** と呼ばれる言語で記述されます。

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、DTDL ファイル内に少なくとも 1 つのモデルを定義することです。

プロジェクトを作成したディレクトリに、 *SampleModel.json* という名前の新しい *.json* ファイルを作成します。 次のファイル本文を貼り付けます。 

```json
{
  "@id": "dtmi:example:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> このチュートリアルのために Visual Studio を使用している場合は、新しく作成した JSON ファイルを選択し、プロパティ インスペクターで *[出力ディレクトリにコピー]* プロパティを *[新しい場合はコピーする]* または *[常にコピーする]* に設定することができます。 これにより、チュートリアルの残りの部分で **F5** キーを使用してプログラムを実行するときに、Visual Studio の既定のパスで JSON ファイルが検出されるようになります。

> [!TIP] 
> あらゆる言語に対応した [DTDL Validator サンプル](/samples/azure-samples/dtdl-validator/dtdl-validator)でモデル ドキュメントをチェックし、DTDL が有効であることを確認できます。 これは DTDL パーサー ライブラリをベースに構築されています。詳細については、" [*モデルを解析および検証する方法*](how-to-parse-models.md)" に関するページを参照してください。

次に、先ほど作成したモデルを Azure Digital Twins インスタンスにアップロードするためのコードを *Program.cs* に追加します。

まず、いくつかの `using` ステートメントをファイルの先頭に追加します。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

次に、C# サービス SDK の非同期メソッドを使用する準備として、非同期実行を許可するように `Main` メソッドのシグネチャを変更します。 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> SDK では、すべての呼び出しの同期バージョンも提供されているため、`async` の使用は厳密には必須ではありません。 このチュートリアルでは、`async` の使用を実践しています。

次に、Azure Digital Twins サービスとやり取りするコードの最初の部分があります。 このコードによって、作成した DTDL ファイルがディスクから読み込まれ、Azure Digital Twins サービス インスタンスにアップロードされます。 

前の手順で追加した承認コードの下に、次のコードを貼り付けます。

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

コマンド ウィンドウで、次のコマンドを使用してプログラムを実行します。 

```cmd/sh
dotnet run
```
このコードに到達したことを示す "Upload a model" が出力に表示されますが、アップロードが成功したかどうかを示す出力はまだありません。

インスタンスに正常にアップロードされたすべてのモデルを示す print ステートメントを追加するには、前のセクションの直後に次のコードを追加します。

```csharp
// Read a list of models back from the service
Console.WriteLine("Models uploaded to the instance:");
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"{md.Id}");
}
```

**この新しいコードをテストするためにプログラムをもう一度実行する前に** 、前回のプログラム実行時にモデルを既にアップロードしたことを思い出してください。 Azure Digital Twins によって、同じモデルを 2 回アップロードすることはできません。そのため、同じモデルを再度アップロードしようとすると、プログラムから例外がスローされます。

この点を考慮して、コマンド ウィンドウでこのコマンドを使用してプログラムを再度実行します。

```cmd/sh
dotnet run
```

プログラムで例外がスローされます。 既にアップロード済みのモデルをアップロードしようとすると、サービスから REST API を介して "無効な要求" エラーが返されます。 その結果、今度は Azure Digital Twins クライアント SDK で、成功以外のすべてのサービス リターン コードに対して例外がスローされます。 

次のセクションでは、このような例外と、ご自分のコードでそれらを処理する方法について説明します。

### <a name="catch-errors"></a>エラーのキャッチ

プログラムのクラッシュを防ぐために、モデル アップロードのコードの周囲に例外コードを追加できます。 次のように、既存のクライアント呼び出し `await client.CreateModelsAsync(typeList)` を try/catch ハンドラー内にラップします。

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

これで、コマンド ウィンドウで `dotnet run` を使用してプログラムを実行すると、エラー コードが返されることがわかります。 モデル作成コードからの出力には、このエラーが表示されます。

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="プログラムの出力。&quot;409:Service request failed.状態:409 (Conflict).&quot; (409: サービス要求が失敗しました。状態: 409 (競合)) というメッセージが表示され、その後に、dtmi:example:SampleModel;1 が既に存在することを示すエラー出力が続きます":::

この時点以降、このチュートリアルでは、サービス メソッドへのすべての呼び出しを try/catch ハンドラー内にラップします。

### <a name="create-digital-twins"></a>デジタル ツインの作成

これで、Azure Digital Twins にモデルがアップロードされたので、このモデル定義を使用して **デジタル ツイン** を作成できます。 [デジタル ツイン](concepts-twins-graph.md)はモデルのインスタンスであり、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 このセクションでは、先ほどアップロードしたモデルに基づいて、いくつかのデジタル ツインを作成します。

`Main` メソッドの末尾に次のコードを追加し、このモデルに基づいて 3 つのデジタル ツインを作成して初期化します。

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
twinData.Contents.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 出力で、 *sampleTwin-0* 、 *sampleTwin-1* 、および *sampleTwin-2* が作成されたことを示す出力メッセージを探します。 

次に、プログラムを再度実行します。 

最初の実行後にツインが既に存在していても、ツインを 2 回目に作成したときにエラーがスローされないことに注意してください。 モデルの作成とは異なり、ツインの作成では、REST レベルで *upsert* セマンティクスを使用して *PUT* 呼び出しを行います。 つまり、ツインが既に存在する場合、同じツインを再び作成しようとすると、単に元のツインが置き換えられます。 エラーはスローされません。

### <a name="create-relationships"></a>リレーションシップの作成

次に、作成したツイン間に **リレーションシップ** を作成することで、それらのツインを接続し、 **ツイン グラフ** を形成することができます。 [ツイン グラフ](concepts-twins-graph.md)は、ご自分の環境全体を表すために使用されます。

**新しい静的メソッド** を `Program` クラスの `Main` メソッドの下に追加します (これで、コードには 2 つのメソッドがあります)。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

次に、`Main` メソッドの末尾に、`CreateRelationship` コードを呼び出す次のコードを追加して、先ほど記述したコードを使用します。

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 出力で、2 つのリレーションシップが正常に作成されたことを示す出力ステートメントを探します。

同じ ID を持つ別のリレーションシップが既に存在する場合、Azure Digital Twins ではリレーションシップを作成できないことに注意してください。そのため、プログラムを複数回実行すると、リレーションシップの作成時に例外が表示されます。 このコードでは、例外をキャッチして無視します。 

### <a name="list-relationships"></a>リレーションシップの一覧表示

次に追加するコードを使用して、作成したリレーションシップの一覧を表示できます。

次の **新しいメソッド** を `Program` クラスに追加します。

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (BasicRelationship rel in results)
        {
            Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

`Main` メソッドの末尾に、`ListRelationships` コードを呼び出す次のコードを追加します。

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 このような出力ステートメントで、作成したすべてのリレーションシップの一覧が表示されます。

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="&quot;Twin sampleTwin-0 is connected to: contains->sampleTwin-1, -contains->sampleTwin-2&quot; (ツイン サンプル Twin-0 が接続されています: contains->sampleTwin-1, -contains->sampleTwin-2) というメッセージを示すプログラム出力":::

### <a name="query-digital-twins"></a>デジタル ツインのクエリ

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。

このチュートリアルで追加するコードの最後のセクションでは、Azure Digital Twins インスタンスに対してクエリを実行します。 この例で使用するクエリでは、インスタンス内のすべてのデジタル ツインが返されます。

この `using` ステートメントを追加して、`JsonSerializer` クラスを使用してデジタル ツイン情報を表示できるようにします。

```csharp
using System.Text.Json;
```

その後、`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Run a query for all twins   
string query = "SELECT * FROM digitaltwins";
AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);

await foreach (BasicDigitalTwin twin in result)
{
    Console.WriteLine(JsonSerializer.Serialize(twin));
    Console.WriteLine("---------------");
}
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 出力には、このインスタンスのすべてのデジタル ツインが表示されます。

## <a name="complete-code-example"></a>完全なコード例

このチュートリアルのこの時点で、Azure Digital Twins に対して基本的なアクションを実行できる完全なクライアント アプリができました。 参照用に、 *Program.cs* 内のプログラムの完全なコードを次に示します。

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
            
            var credential = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            Console.WriteLine("Models uploaded to the instance:");
            AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
            await foreach (DigitalTwinsModelData md in modelDataList)
            {
                Console.WriteLine($"{md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
            twinData.Contents.Add("data", $"Hello World!");
            
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query for all twins   
            string query = "SELECT * FROM digitaltwins";
            AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
            
            await foreach (BasicDigitalTwin twin in result)
            {
                Console.WriteLine(JsonSerializer.Serialize(twin));
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (BasicRelationship rel in results)
                {
                    Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする
 
このチュートリアルで使用したインスタンスは、次に取り組む " [*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 次のチュートリアルに進む場合は、ここで設定した Azure Digital Twins インスタンスを残しておくことができます。
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後に、ローカル コンピューター上に作成したプロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、.NET コンソール クライアント アプリケーションをゼロから作成しました。 このクライアント アプリのコードを記述して、Azure Digital Twins インスタンスに対する基本的なアクションを実行しました。

次のチュートリアルに進み、このようなサンプル クライアント アプリを使用して実行できることを確認してください。 

> [!div class="nextstepaction"]
> [*チュートリアル:サンプル クライアント アプリを使用した基本事項の確認*](tutorial-command-line-app.md)"
