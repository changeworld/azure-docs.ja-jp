---
title: Functions の Azure Cosmos DB バインド
description: Azure Functions で Azure Cosmos DB のトリガーとバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: ffb18ef65bc0d901fe237ec9c4f97fdae43dc472
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Azure Functions の Azure Cosmos DB バインド

この記事では、Azure Functions で [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) のバインドを操作する方法について説明します。 Azure Functions は、Azure Cosmos DB のトリガー、入力、出力のバインドをサポートしています。

> [!NOTE]
> このバインドの元の名称は DocumentDB でした。 Functions バージョン 1.x では、トリガーのみが Cosmos DB に名称が変更され、入力バインド、出力バインド、および NuGet パッケージは DocumentDB のままでした。 [Functions バージョン 2.x](functions-versions.md) では、バインドとパッケージの名称も Cosmos DB に変更されました。 この記事では 1.x の名称を使用します。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>パッケージ

Functions バージョン 1.x の Cosmos DB バインドは、[Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet パッケージで提供されています。 Functions 2.x の場合、パッケージは [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) です。 バインドのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="trigger"></a>トリガー

Azure Cosmos DB のトリガーは [Azure Cosmos DB 変更フィード](../cosmos-db/change-feed.md)を使用して、パーティション間の変更をリッスンします。 変更フィードは、削除ではなく挿入と更新を発行します。 トリガーは、監視対象のコレクション内で挿入または更新が行われるたびに呼び出されます。 

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、特定のデータベースとコレクションからトリガーされる[ C# 関数](functions-dotnet-class-library.md)を示します。

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、Cosmos DB レコードが変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# スクリプト コードを次に示します。
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、Cosmos DB レコードが変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript コードを次に示します。

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[トリガー - 構成](#trigger---configuration)」をご覧ください。 メソッド シグネチャでの `CosmosDBTrigger` 属性の例を次に示します。

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

完全な例については、「[トリガー - C# の例](#trigger---c-example)」を参照してください。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `CosmosDBTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** || `cosmosDBTrigger` に設定する必要があります。 |
|**direction** || `in` に設定する必要があります。 このパラメーターは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** || 変更されるドキュメントの一覧を表す、関数コードで使用する変数の名前。 | 
|**connectionStringSetting**|**ConnectionStringSetting** | 監視されている Azure Cosmos DB アカウントに接続するために使用される接続文字列を含めたアプリ設定の名前。 |
|**databaseName**|**DatabaseName**  | 監視されているコレクションを使用する Azure Cosmos DB データベースの名前。 |
|**collectionName** |**CollectionName** | 監視されているコレクションの名前。 |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (省略可能) リース コレクションを保持するサービスへの接続文字列を含むアプリ設定の名前。 この値を設定しない場合、`connectionStringSetting` という値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 リース コレクションの接続文字列には書き込み権限が必要です。|
|**leaseDatabaseName** |**LeaseDatabaseName** | (省略可能) リースの保存のために使用するコレクションを保持しているデータベースの名前。 この値を設定しない場合、`databaseName` の設定の値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 |
|**leaseCollectionName** | **LeaseCollectionName** | (省略可能) リースの保存のために使用するコレクションの名前。 この値を設定しない場合、`leases` という値が使用されます。 |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (省略可能) `true` を設定すると、リース コレクションが存在していない場合に自動でリース コレクションを作成します。 既定値は `false` です。 |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (省略可能) リース コレクションの作成に割り当てる要求ユニットの量を定義します。 この設定は、`createLeaseCollectionIfNotExists` が `true` に設定されている場合のみ使用できます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (省略可能) 設定すると、この関数のリース コレクション内に作成されたリースにプレフィックスが追加され、2 つの独立した Azure Functions が、異なるプレフィックスを使用することで同じリース コレクションを効率的に共有できます。
|**feedPollDelay**| **FeedPollDelay**| (省略可能) 設定すると、現在の変更がすべて排除された後、パーティションでフィードの新しい変更をポーリングするまでの遅延時間がミリ秒単位で定義されます。 既定値は 5,000 (5 秒) です。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (省略可能) 設定すると、パーティションが既知のホスト インスタンス間で均等に分散されているかどうかを計算するタスクを開始する間隔がミリ秒単位で定義されます。 既定値は 13,000 (13 秒) です。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (省略可能) 設定すると、パーティションを表すリースでリースを取得する間隔がミリ秒単位で定義されます。 この間隔内にリースが更新されない場合、リースは期限切れとなり、パーティションの所有権は別のインスタンスに移動します。 既定値は 60,000 (60 秒) です。
|**leaseRenewInterval**| **LeaseRenewInterval**| (省略可能) 設定すると、インスタンスが現在保持しているパーティションのすべてのリースの更新間隔がミリ秒単位で定義されます。 既定値は 17,000 (17 秒) です。
|**checkpointFrequency**| **CheckpointFrequency**| (省略可能) 設定すると、リース チェックポイントの間隔がミリ秒単位で定義されます。 既定値は、常に成功した関数呼び出しの後になります。
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (省略可能) 設定すると、関数呼び出しごとに受信するアイテムの最大量がカスタマイズされます。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

トリガーは、パーティションに_リース_を保存するために使用する 2 つ目のコレクションを必要とします。 トリガーが機能するためには、監視されるコレクションと、リースを含むコレクションの両方が使用できる必要があります。

 >[!IMPORTANT]
 > 複数の関数が同じコレクションの Cosmos DB トリガーを使用するように構成している場合、各関数では専用のリース コレクションを使用する必要があります。 専用のリース コレクションを使用していない場合は、関数のうちの 1 つしかトリガーされません。 

トリガーは、ドキュメントが更新または挿入されたかどうかを示すわけではなく、単にドキュメント自体を提供します。 更新と挿入を別の方法で処理する必要がある場合は、挿入または更新のタイムスタンプを実装することでこれを実現できます。

## <a name="input"></a>入力

Azure Cosmos DB 入力バインドでは、1 つ以上の Azure Cosmos DB ドキュメントを取得して関数の入力パラメーターに渡します。 ドキュメント ID またはクエリ パラメーターは、関数を呼び出したトリガーに基づいて決定することができます。 

>[!NOTE]
> Cosmos DB アカウントで MongoDB API を使用している場合は、Azure Cosmos DB の入力または出力バインディングを使用しないでください。 データの破損を引き起こす可能性があります。

## <a name="input---example-1"></a>入力 - 例 1

単一のドキュメントを読み取る言語固有の例を参照してください。

* [C#](#input---c-example)
* [C# スクリプト (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>入力 - C# の例

次の例は、特定のデータベースとコレクションから 1 つのドキュメントを取得する[ C# 関数](functions-dotnet-class-library.md)を示します。 

最初に、`CarReview` インスタンスの `Id` と `Maker` の値をキューに渡します。 Cosmos DB のバインディングは、キュー メッセージからの `Id` と `Maker` を使って、データベースからドキュメントを取得します。

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

次に `CarReview` POCO を示します。

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>入力 - C# スクリプトの例

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>入力 - F# の例

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [F# スクリプト関数](functions-reference-fsharp.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

F# コードを次に示します。

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

この例には、`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルが必要です。

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

`project.json` ファイルの追加方法については、[のパッケージ管理](functions-reference-fsharp.md#package)に関するセクションを参照してください。

### <a name="input---javascript-example"></a>入力 - JavaScript の例

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>入力 - 例 2

複数のドキュメントを読み取る言語固有の例を参照してください。

* [C#](#input---c-example-2)
* [C# スクリプト (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>入力 - C# の例 2

次の例は、SQL クエリを実行する[ C# 関数](functions-dotnet-class-library.md)を示します。 `SqlQuery` パラメーターを使用するには、`Microsoft.Azure.WebJobs.Extensions.DocumentDB` NuGet パッケージの最新のベータ版をインストールする必要があります。

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>入力 - C# スクリプトの例 2

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

### <a name="input---javascript-example-2"></a>入力 - JavaScript の例 2

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

## <a name="input---attributes"></a>入力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、[この後の構成に関するセクション](#input---configuration)をご覧ください。 

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `DocumentDB` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**     || `documentdb` に設定する必要があります。        |
|**direction**     || `in` に設定する必要があります。         |
|**name**     || 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** |**DatabaseName** |ドキュメントを含むデータベース。        |
|**collectionName** |**CollectionName** | ドキュメントを含むコレクションの名前。 |
|**id**    | **Id** | 取得するドキュメントの ID。 このプロパティは、[バインド式](functions-triggers-bindings.md#binding-expressions-and-patterns)をサポートしています。 **id** プロパティと **sqlQuery** プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。 |
|**sqlQuery**  |**SqlQuery**  | 複数のドキュメントを取得するときに使用する Azure Cosmos DB SQL クエリ。 このプロパティは、次の例のように実行時のバインドをサポートします。`SELECT * FROM c where c.departmentId = {departmentId}` **id** プロパティと **sqlQuery** プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。|
|**connection**     |**ConnectionStringSetting**|Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |
|**partitionKey**|**PartitionKey**|参照用のパーティション キー値を指定します。 バインディング パラメーターを含めることもできます。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>入力 - 使用方法

C# および F# 関数では、関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。 

JavaScript 関数の場合、関数の終了時に更新が自動的に行われることはありません。 代わりに、`context.bindings.<documentName>In` と `context.bindings.<documentName>Out` を使用して、更新を行います。 [JavaScript の例](#input---javascript-example)を参照してください。

## <a name="output"></a>出力

Azure Cosmos DB 出力バインドを使用すると、Azure Cosmos DB データベースに新しいドキュメントを記述できます。 

>[!NOTE]
> Cosmos DB アカウントで MongoDB API を使用している場合は、Azure Cosmos DB の入力または出力バインディングを使用しないでください。 データの破損を引き起こす可能性があります。

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、キュー ストレージからのメッセージで提供されるデータを使用して、ドキュメントをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

複数のドキュメントを作成するために `ICollector<T>` または `IAsyncCollector<T>` にバインドできます。`T` は、サポートされている型のいずれかです。

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

F# コードを次に示します。

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

この例には、`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルが必要です。

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

`project.json` ファイルの追加方法については、[のパッケージ管理](functions-reference-fsharp.md#package)に関するセクションを参照してください。

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[出力 - 構成](#output---configuration)」をご覧ください。 メソッド シグネチャでの `DocumentDB` 属性の例を次に示します。

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `DocumentDB` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**     || `documentdb` に設定する必要があります。        |
|**direction**     || `out` に設定する必要があります。         |
|**name**     || 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** | **DatabaseName**|ドキュメントが作成されたコレクションを含むデータベース。     |
|**collectionName** |**CollectionName**  | ドキュメントが作成されたコレクションの名前。 |
|**createIfNotExists**  |**CreateIfNotExists**    | コレクションが存在しないときに作成するかどうかを示すブール値。 既定値は *false* です。新しいコレクションは予約済みのスループットで作成され、これが価格に影響を及ぼすためです。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。  |
|**partitionKey**|**PartitionKey** |`CreateIfNotExists` が true の場合に、作成されるコレクションのパーティション キー パスを定義します。|
|**collectionThroughput**|**CollectionThroughput**| `CreateIfNotExists` が true の場合に、作成されるコレクションの[スループット](../cosmos-db/set-throughput.md)を定義します。|
|**connection**    |**ConnectionStringSetting** |Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

既定では、関数の出力パラメーターに書き込むと、ドキュメントがデータベースに作成されます。 このドキュメントには、自動的に生成された GUID がドキュメント ID として割り当てられます。 出力ドキュメントのドキュメント ID を指定するには、出力パラメーターに渡された JSON オブジェクトで `id` プロパティを指定します。 

> [!Note]  
> 既存のドキュメントの ID を指定した場合、既存のドキュメントは新しい出力ドキュメントによって上書きされます。 

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Cosmos DB | [CosmosDB のエラー コード](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cosmos DB トリガーを使用するクイックスタートに進む](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Cosmos DB のサーバーレス データベース コンピューティングの詳細情報](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
