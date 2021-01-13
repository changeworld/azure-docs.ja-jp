---
title: Azure Digital Twins の API および SDK を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins の API の操作方法 (SDK 経由を含む) を参照してください。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3c880d570cfa6e1b1388e59557836a3070d7cdc7
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862540"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins の API および SDK を使用する

Azure Digital Twins には、インスタンスとその要素を管理するための **コントロール プレーン API** と **データ プレーン API** の両方が用意されています。 
* コントロール プレーン API は、[Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) API であり、インスタンスの作成や削除などのリソース管理操作に対応しています。 
* データ プレーン API は、Azure Digital Twins API であり、モデル、ツイン、グラフの管理などのデータ管理操作に使用されます。

この記事では、使用可能な API の概要と、API を操作するためのメソッドについて説明します。 REST API は、関連付けられている Swagger を使って直接使用することも ([Postman](how-to-use-postman.md) などのツールを使用)、SDK を通して使用することもできます。

## <a name="overview-control-plane-apis"></a>概要: コントロール プレーン API

コントロール プレーン API は、[ARM](../azure-resource-manager/management/overview.md) API であり、Azure Digital Twins インスタンス全体を管理するために使用されます。そのため、インスタンス全体の作成や削除などの操作に対応しています。 また、エンドポイントの作成と削除にも使用します。

最新のコントロール プレーン API のバージョンは、_**2020-12-01**_ です。

コントロール プレーン API を使用するには
* [コントロール プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)の最新の Swagger フォルダーを参照することで、API を直接呼び出すことができます。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。
* 現在、コントロール API の SDK には、次の言語でアクセスできます。
  - [ **.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([参照 [自動生成]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([ソース](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_10_31/azure-mgmt-digitaltwins/1.0.0/jar) ([参照 [自動生成]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([ソース](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/mgmt-v2020_10_31))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([ソース](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

また、コントロール プレーン API の演習を行うには、[Azure portal](https://portal.azure.com) および [CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="overview-data-plane-apis"></a>概要: データ プレーン API

データ プレーン API は、Azure Digital Twins API であり、Azure Digital Twins インスタンス内の要素を管理するために使用されます。 これには、ルートの作成、モデルのアップロード、リレーションシップの作成、ツインの管理などの操作が含まれます。 これらは、主に次のカテゴリに分けることができます。
* **DigitalTwinModels** - DigitalTwinModels カテゴリには、Azure Digital Twins のインスタンスの [モデル](concepts-models.md)を管理するための API が含まれています。 管理アクティビティには、DTDL で作成されたモデルのアップロード、検証、取得、および削除が含まれます。
* **DigitalTwins** - DigitalTwins カテゴリには、開発者が Azure Digital Twins のインスタンスで [Digital Twins](concepts-twins-graph.md) とその関係を作成、変更、および削除するための API が含まれています。
* **Query** - 開発者は、Query カテゴリを使用して、複数のリレーションシップにわたる [ツイングラフで一連の Digital Twins を検索できます](how-to-query-graph.md)。
* **Event Routes** - Event Routes カテゴリには、システムを通してダウンストリーミング サービスに [データをルーティングする](concepts-route-events.md) API が含まれています。

最新のデータ プレーン API のバージョンは、_**2020-10-31**_ です。

データ プレーン API を使用するには
* API を直接呼び出すには、次の操作を実行します。
   - [データ プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)の最新の Swagger フォルダーを参照する。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。 
   - [API リファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照する。
* **.NET (C#)** SDK を使用できます。 .NET SDK は次の方法で使用します。
   - NuGet からパッケージを表示して追加する。[Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core) を表示する。 
   - [SDK のリファレンス ドキュメント](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)を確認する。
   - GitHub で、サンプルのフォルダーを含む SDK ソース [Azure IoT Digital Twins client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) を探す。 
   - 詳細な情報と使用例については、この記事の「[ *.NET (C#) SDK (データ プレーン)*](#net-c-sdk-data-plane)」セクションを参照してください。
* **Java** SDK を使用できます。 Java SDK を使用するには、次のようにします。
   - Maven からパッケージを表示してインストールする。[`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - [SDK のリファレンス ドキュメント](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable)を確認する
   - GitHub で SDK のソースを探す。[Azure IoT Digital Twins client library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* **JavaScript** SDK を使用できます。 JavaScript SDK を使用するには、次のようにします。
   - npm からパッケージを表示してインストールする。[JavaScript 用 Azure Digital Twins コア クライアント ライブラリ](https://www.npmjs.com/package/@azure/digital-twins-core)。
   - [SDK のリファレンス ドキュメント](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true)を確認する。
   - GitHub で SDK のソースを探す。[JavaScript 用 Azure Digital Twins コア クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* **Python** SDK を使用できます。 Python SDK を使用するには、次のようにします。
   - PyPi からパッケージを表示してインストールする。[Azure Azure Digital Twins Core client library for Python](https://pypi.org/project/azure-digitaltwins-core/)。
   - [SDK のリファレンス ドキュメント](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)を確認する。
   - GitHub で SDK のソースを探す。[Azure Digital Twins Core client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* AutoRest を使用して、別の言語用の SDK を生成することができます。 ["*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*"](how-to-create-custom-sdks.md) の手順に従ってください。

また、データ プレーン API の演習を行うには、[CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (データ プレーン)

Azure Digital Twins .NET (C#) SDK は、Azure SDK for .Net に含まれています。 これはオープン ソースであり、Azure Digital Twins データ プレーン API に基づいています。

> [!NOTE]
> SDK の設計の詳細については、[Azure SDK の一般的な設計原則](https://azure.github.io/azure-sdk/general_introduction.html)と特定の [.NET 設計ガイドライン](https://azure.github.io/azure-sdk/dotnet_introduction.html)を参照してください。

SDK を使用するには、NuGet パッケージ **Azure.DigitalTwins.Core** をプロジェクトに含めます。 また、**Azure.Identity** パッケージの最新バージョンも必要です。 Visual Studio では、NuGet パッケージ マネージャーを使用してこれらのパッケージを追加できます。そのためには、 *[ツール]、[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]* の順に選択します。 または、.NET コマンド ライン ツールと次の NuGet パッケージ リンクにあるコマンドを使用し、プロジェクトにこれらを追加できます。
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 これは、[.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) のパッケージです。 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity)。 このライブラリでは、Azure に対する認証を支援するツールが提供されます。

実際の API の使用方法の詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

### <a name="net-sdk-usage-examples"></a>.NET SDK の使用例

ここでは、.NET SDK の使用方法を示すコード サンプルをいくつか紹介します。

サービスに対する認証を実行する:

```csharp
// Authenticate against the service and create a client
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
```

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

モデルをアップロードし、モデルを一覧表示する:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Twins を作成して照会する:

```csharp
// Initialize twin metadata
BasicDigitalTwin updateTwinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.Contents.Add("data", "Hello World!");
try {
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("firstTwin", updateTwinData);
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

このサンプル コード アプリの詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

その他のサンプルについては、[.NET (C#) SDK 用 GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)を参照してください。

#### <a name="serialization-helpers"></a>シリアル化のヘルパー

シリアル化のヘルパーは、基本情報にアクセスするためのツイン データを、迅速に作成または逆シリアル化するために SDK 内で使用できるヘルパー関数です。 コア SDK メソッドは、既定でツイン データを JSON として返すため、これらのヘルパー クラスを使用して、ツイン データをさらに分割するのに役立ちます。

使用できるヘルパー クラスは次のとおりです。
* `BasicDigitalTwin`:デジタル ツインのコアデータを表します。
* `BasicRelationship`:リレーションシップのコア データを表します。
* `UpdateOperationUtility`:Update 呼び出しで使用される JSON パッチ情報を表します。
* `WriteableProperty`:プロパティのメタデータを表します。

##### <a name="deserialize-a-digital-twin"></a>デジタル ツインを逆シリアル化する

`System.Test.Json` や `Newtonsoft.Json` といった任意の JSON ライブラリを使用して、ツイン データをいつでも逆シリアル化できます。 ツインにアクセスする際には、ヘルパー クラスを使用すると便利です。

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

また、`BasicDigitalTwin` ヘルパー クラスを使用すると、ツインで定義されたプロパティに `Dictionary<string, object>` を介してアクセスできます。 ツインのプロパティを一覧表示するには、次のコードを使用します。

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>デジタル ツインを作成する

`BasicDigitalTwin` クラスを使用すると、ツイン インスタンスを作成するためのデータを準備できます。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

前述のコードは、次の "手動" バリアントと同じです。

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

##### <a name="deserialize-a-relationship"></a>リレーションシップを逆シリアル化する

リレーションシップ データはいつでも任意の型に逆シリアル化できます。 リレーションシップへの基本的なアクセスには、`BasicRelationship` 型を使用します。

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

また、`BasicRelationship` ヘルパー クラスを使用すると、リレーションシップで定義されたプロパティに `IDictionary<string, object>` を介してアクセスできます。 プロパティを一覧表示するには、次のコードを使用します。

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>リレーションシップを作成する

`BasicRelationship` クラスを使用すると、ツイン インスタンスでリレーションシップを作成するためのデータを準備できます。

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.Properties = props;
client.CreateOrReplaceRelationshipAsync("mySourceTwin", "rel001", rel);
```

##### <a name="create-a-patch-for-twin-update"></a>ツインを更新するための修正プログラムを作成する

ツインとリレーションシップの Update 呼び出しでは、[JSON Patch](http://jsonpatch.com/) 構造を使用します。 JSON Patch 操作のリストを作成するには、下に示すように `JsonPatchDocument` を使用します。

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", 25.0);
updateTwinData.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
updateTwinData.AppendRemoveOp("/Humidity");

client.UpdateDigitalTwin("myTwin", updateTwinData);
```

## <a name="general-apisdk-usage-notes"></a>一般的な API/SDK の使用上の注意

> [!NOTE]
> 現時点では、Azure Digital Twins では **クロス オリジン リソース共有 (CORS)** がサポートされないことに注意してください。 影響と解決方法の詳細については、[ *「クロス オリジン リソース共有 (CORS)」*](concepts-security.md#cross-origin-resource-sharing-cors)セクション (*概念:Azure Digital Twins ソリューションのセキュリティ* に関するページを参照してください。

次のリストに、API と SDK の使用に関する追加詳細情報および一般的なガイドラインを示します。

* Postman などの HTTP REST テスト ツールを使用して、Azure Digital Twins の API を直接呼び出すことができます。 このプロセスの詳細については、"[*Postman を使用して要求を作成する方法*](how-to-use-postman.md)" に関する記事を参照してください。
* SDK を使用するには、`DigitalTwinsClient` クラスをインスタンス化します。 コンストラクターには、`Azure.Identity` パッケージ内のさまざまな認証方法で取得できる資格情報が必要です。 `Azure.Identity` の詳細については、[名前空間に関するドキュメント](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)を参照してください。 
* 作業を開始するときには `InteractiveBrowserCredential` が役立ちますが、[マネージド ID](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) の資格情報など、他にもいくつかのオプションがあります。これは、Azure Digital Twins に対して、[MSI を使用した Azure Functions のセットアップ](../app-service/overview-managed-identity.md?tabs=dotnet)を認証する際に使用できます。 `InteractiveBrowserCredential` の詳細については、[クラスのドキュメント](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)を参照してください。
* すべてのサービス API 呼び出しは、`DigitalTwinsClient` クラスのメンバー関数として公開されます。
* すべてのサービス関数には、同期バージョンと非同期バージョンが存在します。
* すべてのサービス関数は、400 以上のリターン状態に対して例外をスローします。 `try` セクションに呼び出しをラップし、少なくとも `RequestFailedExceptions` をキャッチします。 この種類の例外の詳細については、[こちら](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet)を参照してください。
* ほとんどのサービス メソッドは `Response<T>` (または非同期呼び出しの場合は `Task<Response<T>>`) を返します。 `T` は、サービス呼び出しで返されるオブジェクトのクラスです。 [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet) クラスは、サービスの戻り値をカプセル化し、その `Value` フィールドに戻り値を表示します。  
* ページングされた結果を含むサービス メソッドは、結果として `Pageable<T>` または `AsyncPageable<T>` を返します。 `Pageable<T>` クラスの詳細については、[こちら](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview)を参照してください。`AsyncPageable<T>` の詳細については、[こちら](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview)を参照してください。
* `await foreach` ループを使用して、ページングした結果を反復処理できます。 この処理の詳細については、[こちら](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)を参照してください。
* 基になる SDK は `Azure.Core` です。 SDK のインフラストラクチャと種類については、[Azure 名前空間のドキュメント](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview)を参照してください。

サービス メソッドは、可能な限り、厳密に型指定されたオブジェクトを返します。 ただし、Azure Digital Twins は、実行時にユーザーによって構成されたカスタム モデル (サービスにアップロードされた DTDL モデルを使用) に基づいているため、多くのサービス API はツイン データを JSON 形式で取得して返します。

## <a name="monitor-api-metrics"></a>API メトリックの監視

要求、待機時間、失敗率などの API メトリックは、[Azure portal](https://portal.azure.com/) で見ることができます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 Azure Digital Twins インスタンスのメニューから **[メトリック]** オプションを選択して、 *[メトリック]* ページを表示します。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

## <a name="next-steps"></a>次のステップ

Postman を使用して API に直接要求を行う方法を参照してください。
* [*方法: Postman で要求を行う*](how-to-use-postman.md)

または、このチュートリアルを使用してクライアント アプリを作成し、.NET SDK を使用する練習を行います。
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)