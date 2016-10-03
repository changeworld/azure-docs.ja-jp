<properties
	pageTitle="Azure Functions における Azure Storage のトリガーとバインド | Microsoft Azure"
	description="Azure Functions で Azure Storage のトリガーとバインドを使用する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Azure Functions における Azure Storage のトリガーとバインド

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Storage のトリガーとバインドを構成したりコーディングしたりする方法について説明します。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Azure Storage キュー トリガー

#### ストレージ キュー トリガーの function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: キューまたはキュー メッセージの関数コードで使用される変数名。
- `queueName`: ポーリングするキューの名前。キューの名前付け規則については、「[キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)」を参照してください。
- `connection`: ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは、AzureWebJobsStorage アプリ設定で指定される Function App の既定のストレージ接続文字列で動作します。
- `type`: *queueTrigger* に設定する必要があります。
- `direction`: *in* に設定する必要があります。

ストレージ キュー トリガーの *function.json* の例を次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### キュー トリガーにサポートされた型

キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* オブジェクト (JSON)
* String
* Byte array
* `CloudQueueMessage` (C#)

#### キュー トリガー メタデータ

関数にキュー メタデータを取得するには、次の変数名を使用します。

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (文字列としてキュー メッセージ テキストを取得する別の方法)

この C# コードの例では、ログ キュー メタデータを取得し、記録します。

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### 有害キュー メッセージの処理

関数の失敗を引き起こす内容を含むメッセージは*有害メッセージ*と呼ばれます。関数が失敗してもキュー メッセージは削除されず、最終的には回収されて、このサイクルを繰り返します。SDK では一定数繰り返し送信されると自動的にそのサイクルを中断します。また手動でも処理できます。

SDKでは、キュー メッセージを処理する関数を最大 5 回呼び出します。5 回目が失敗すると、メッセージは有害メッセージ キューに移動します。

有害キューには *{originalqueuename}*-poison という名前が付けられます。メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

有害メッセージを手動で処理する場合は、処理のためにメッセージが取得された回数を、`dequeueCount` を確認して取得できます。

## <a id="storagequeueoutput"></a>Azure Storage キュー出力バインド

#### ストレージ キュー出力バインドの function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: キューまたはキュー メッセージの関数コードで使用される変数名。
- `queueName`: キューの名前。キューの名前付け規則については、「[キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)」を参照してください。
- `connection`: ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは、AzureWebJobsStorage アプリ設定で指定される Function App の既定のストレージ接続文字列で動作します。
- `type`: *queue* に設定する必要があります。
- `direction`: *out* に設定する必要があります。

キュー トリガーを使用してキュー メッセージを書き込むストレージ キュー出力バインドの *function.json* の例を次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### キュー出力バインドにサポートされた型

`queue` バインドは、次の型をキュー メッセージにシリアル化できます。

* Object (C# の `out T` で、関数が終了したときにパラメーターが null である場合は、null オブジェクトでメッセージを作成します)
* String (C# の `out string` で、関数が終了したときにパラメーター値が null でない場合は、キュー メッセージを作成します)
* Byte array (C# の `out byte[]` で、string と同様に動作)
* `out CloudQueueMessage` (C#、string と同様に動作)

また、C# では、`T` がいずれかのサポートされている型である場合、`ICollector<T>` または `IAsyncCollector<T>` にバインドすることもできます。

#### キュー出力バインドのコード例

この C# コード例では、入力キュー メッセージごとに 1 つの出力キュー メッセージを書き込みます。

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

次の C# コード例では、`ICollector<T>` を使用して複数のメッセージを書き込みます (非同期関数で `IAsyncCollector<T>` を使用)。

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Azure Storage BLOB トリガー

#### ストレージ BLOB トリガーの function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: BLOB の関数コードで使用される変数名。
- `path`: 監視するコンテナーを指定するパスと、必要に応じて BLOB 名のパターンを指定します。
- `connection`: ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは、AzureWebJobsStorage アプリ設定で指定される Function App の既定のストレージ接続文字列で動作します。
- `type`: *blobTrigger* に設定する必要があります。
- `direction`: *in* に設定する必要があります。

samples-workitems コンテナーに追加される BLOB を監視するストレージ BLOB トリガーの *function.json* の例を次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### BLOB トリガーにサポートされた型

ノードまたは C# の関数では、BLOB は次の型のいずれかに逆シリアル化することができます。

* オブジェクト (JSON)
* 文字列

C# 関数の場合は、次の型のいずれかにもバインドできます。

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) によって逆シリアル化されるその他の種類

#### BLOB トリガーの C# コードの例

この C# コード例では、監視対象のコンテナーに追加される各 BLOB の内容を記録します。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### BLOB トリガー名のパターン

BLOB 名のパターンは、`path` プロパティで指定できます。次に例を示します。

```json
"path": "input/original-{name}",
```

このパスは、*input* コンテナーの *original-Blob1.txt* という名前の BLOB を探し、関数コード内の `name` 変数の値は `Blob1` になります。

別の例:

```json
"path": "input/{blobname}.{blobextension}",
```

このパスは、*original-Blob1.txt* という名前の BLOB を探し、関数コード内の `blobname` 変数および `blobextension` 変数の値は *original-Blob1* と *txt* になります。

関数をトリガーする BLOB の型を制限するには、ファイル拡張子の値が固定されたパターンを指定します。`path` を *samples/{name}.png* に設定した場合、*samples* コンテナー内の *.png* BLOB のみが関数をトリガーします。

名前に波括弧がある BLOB 名に新しいパターンを指定する必要がある場合は、波括弧を二重にします。たとえば、以下のような名前を持つ *イメージ*コンテナーに blob を見つける場合は、

		{20140101}-soundfile.mp3

`path` プロパティには以下を使用します。

		images/{{20140101}}-{name}

この例では、`name` 変数の値は、*soundfile.mp3* になります。

#### BLOB の配信確認メッセージ

Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。これは*BLOB の配信確認メッセージ*を維持して、特定の BLOB バージョンが処理されているかどうかを判断するためです。

BLOB の配信確認メッセージは、AzureWebJobsStorage 接続文字列が指定した Azure ストレージ アカウントの *azure-webjobs-hosts* という名前のコンテナーに格納されています。BLOB の配信確認メッセージには次の情報が含まれています。

* BLOB に対して呼び出された関数 ("*{Function App 名}*.Functions.*{関数名}*"。たとえば、"functionsf74b96f7.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、 *azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

#### 有害 BLOB の処理

BLOB トリガー関数が失敗した場合、失敗が一時的なエラーによって発生した場合は、SDK は再度関数を呼び出します。失敗が BLOB のコンテンツによって発生した場合は、BLOB の処理を試みるたびに関数は失敗します。既定では、SDK は特定の BLOB に対して最大 5 回、関数を呼び出します。5 回目が失敗すると、SDK はメッセージは、 *webjobs-blobtrigger-poison* という名前のキューにメッセージを追加します。

有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *{Function App 名}*.Functions.*{関数名}*)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

#### 大規模なコンテナーをポーリングする BLOB

トリガーが監視する BLOB コンテナーに 10,000 を超える BLOB が含まれる場合は、Functions ランタイムによりログ ファイルがスキャンされ、新しいまたは変更された BLOB が監視されます。このプロセスはリアルタイムではありません。関数は、BLOB が作成されてから数分またはそれ以上経過しないとトリガーされない可能性があります。また、[ストレージ ログの作成は "ベスト エフォート"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ベースで行われます。そのため、すべてのイベントがキャプチャされる保証はありません。ある条件下では、ログが欠落する可能性があります。大規模なコンテナーで BLOB トリガーの速度と信頼性の制限がアプリケーションで許容されない場合は、BLOB を作成するときにキュー メッセージを作成し、BLOB トリガーではなくキュー トリガーを使って BLOB を処理することをお勧めします。
 
## <a id="storageblobbindings"></a>Azure Storage BLOB の入力バインドと出力バインド

#### ストレージ BLOB 入力または出力バインドの function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: BLOB の関数コードで使用される変数名。
- `path`: BLOB を読み込むまたは BLOB を書き込むコンテナーを指定するパスと、必要に応じて BLOB 名のパターンを指定します。
- `connection`: ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、バインドは、AzureWebJobsStorage アプリ設定で指定される Function App の既定のストレージ接続文字列で動作します。
- `type`: *blob* に設定する必要があります。
- `direction`: *in* または *out* に設定します。

キュー トリガーを使用して BLOB をコピーするストレージ BLOB の入力バインドと出力バインドの *function.json* の例を次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### BLOB の入力および出力がサポートする型

`blob` バインドは、Node.js または C# 関数で次の型をシリアル化または逆シリアル化できます。

* Object (出力 BLOB の場合は C# の `out T`: 関数が終了したときに、パラメーター値が null の場合に null オブジェクトとして BLOB を作成します)
* String (出力 BLOB の場合は、C# の `out string`: 関数を返されたときに、文字列パラメーターが null 以外の場合にのみ BLOB を作成します)

C# 関数の場合は、次の型にもバインドできます。

* `TextReader` (入力のみ)
* `TextWriter` (出力のみ)
* `Stream`
* `CloudBlobStream` (出力のみ)
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

#### BLOB 出力の C# コードの例

この C# コード例では、名前をキュー メッセージで受信した BLOB をコピーします。

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Azure Storage テーブルの入力バインドと出力バインド

#### ストレージ テーブルの function.json

*function.json* では、次のプロパティを指定します。

- `name`: テーブル バインドの関数コードで使用される変数名。
- `tableName`: テーブルの名前。
- `partitionKey` および `rowKey`: 一緒に使用して、C# またはノード関数の単一エンティティを読み取るか、ノード関数の単一のエンティティを書き込みます。
- `take`: ノード関数でのテーブル入力のために読み取る行の最大数。
- `filter`: ノード関数のテーブル入力の OData フィルター式。
- `connection`: ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、バインドは、AzureWebJobsStorage アプリ設定で指定される Function App の既定のストレージ接続文字列で動作します。
- `type`: *table* に設定する必要があります。
- `direction`: *in* または *out* に設定します。

次の例の *function.json* では、キュー トリガーを使用して 1 つのテーブル行を読み取ります。この JSON は、ハードコーディングされたパーティション キー値を提供すると共に、キュー メッセージから行キーを取得するように指定しています。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### ストレージ テーブルの入力および出力にサポートされた型

`table` バインドは、Node.js または C# 関数でオブジェクトをシリアル化または逆シリアル化できます。オブジェクトには、RowKey と PartitionKey プロパティがあります。

C# 関数の場合は、次の型にもバインドできます。

* `T` が `ITableEntity` を実装する場合の `T`
* `IQueryable<T>` (入力のみ)
* `ICollector<T>` (出力のみ)
* `IAsyncCollector<T>` (出力のみ)

#### ストレージ テーブル バインドのシナリオ

テーブル バインドは、次のシナリオをサポートしています。

* C# またはノード関数での単一行の読み取り。

	`partitionKey` と `rowKey` を設定します。`filter` および `take` プロパティは、このシナリオでは使用しません。

* C# 関数での複数行の読み取り。

	Functions ランタイムは、テーブルにバインドされている `IQueryable<T>` オブジェクトを指定します。型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装する必要があります。`partitionKey`、`rowKey`、`filter`、および `take` の各プロパティは、このシナリオでは使用しません。必要なフィルター処理は、`IQueryable` オブジェクトを使用して実行できます。

* ノード関数での複数行の読み取り。

	`filter` と `take` プロパティを設定します。`partitionKey` と `rowKey` は設定しません。

* C# 関数での 1 つまたは複数の行の書き込み。

	Functions ランタイムは、テーブルにバインドされた `ICollector<T>` または `IAsyncCollector<T>` を指定します。ここで、`T` は追加するエンティティのスキーマを指定します。型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装するのが一般的ですが、必須ではありません。`partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。

#### ストレージ テーブルの例: C# またはノードでの 1 つのテーブル エンティティの読み取り

次の C# コード例では、既出の *function.json* ファイルを使用して、単一のテーブル エンティティを読み取ります。キュー メッセージには行キー値があり、テーブル エンティティは *run.csx* ファイルで定義された型に読み込まれます。型は `PartitionKey` と `RowKey` プロパティを含み、`TableEntity` から派生しません。

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

次の F# コード例でも、上記の *function.json* ファイルを使用して、単一のテーブル エンティティを読み取ります。

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

次のノード コード例でも、上記の *function.json* ファイルを使用して、単一のテーブル エンティティを読み取ります。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### ストレージ テーブルの例: C での複数のテーブル エンティティの読み取り# 

次の *function.json* および C# コード例では、キュー メッセージに指定されたパーティション キーのエンティティを読み取ります。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# コードは、エンティティ型が `TableEntity` から派生できるように、Azure Storage SDK に参照を追加します。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### ストレージ テーブルの例: C でのテーブル エンティティの作成# 

次の例の *function.json* と *run.csx* では、C# でテーブル エンティティを書き込む方法を示します。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### ストレージ テーブルの例: F でのテーブル エンティティの作成#

次の例の *function.json* と *run.fsx* では、F# でテーブル エンティティを書き込む方法を示します。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### ストレージ テーブルの例: ノードでのテーブル エンティティの作成

次の例の *function.json* と *run.csx* では、ノードでテーブル エンティティを書き込む方法を示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## 次のステップ

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->