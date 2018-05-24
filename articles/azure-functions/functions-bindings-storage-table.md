---
title: Azure Functions における Azure Table Storage のバインド
description: Azure Functions で Azure Table のバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 1c8cee149e99786b58e4584e5e7b508b1040389d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions における Azure Table Storage のバインド

この記事では、Azure Functions で Azure Table Storage のバインドを使用する方法について説明します。 Azure Functions は、Azure Table ストレージの入力および出力のバインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>パッケージ

Table ストレージ バインディングは [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージで提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>入力

Azure Table Storage の入力バインドを使用して、Azure Storage アカウントのテーブルを読み取ります。

## <a name="input---example"></a>入力 - 例

言語固有の例をご覧ください。

* [C# 単一エンティティの読み取り](#input---c-example-1)
* [C# 複数エンティティの読み取り](#input---c-example-2)
* [C# スクリプト - 単一エンティティの読み取り](#input---c-script-example-1)
* [C# スクリプト - 複数エンティティの読み取り](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>入力 - C# の例 1

次の例は、1 つのテーブル行を読み取る [C# 関数](functions-dotnet-class-library.md)を示しています。 

行キー値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>入力 - C# の例 2

次の例は、複数のテーブル行を読み取る [C# 関数](functions-dotnet-class-library.md)を示しています。 `MyPoco` クラスは、`TableEntity` から派生しています。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-script-example-1"></a>入力 - C# スクリプトの例 1

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は、キュー トリガーを使用して単一のテーブル行を読み取ります。 

*function.json* ファイルには `partitionKey` と `rowKey` が指定されています。 `rowKey` 値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

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

### <a name="input---c-script-example-2"></a>入力 - C# スクリプトの例 2

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は、キュー メッセージに指定されているパーティション キーのエンティティを読み取ります。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

この C# スクリプト コードは、エンティティ型が `TableEntity`から派生できるように、Azure Storage SDK に参照を追加します。

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

### <a name="input---f-example"></a>入力 - F# の例

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [F# スクリプト](functions-reference-fsharp.md) コードを示しています。 この関数は、キュー トリガーを使用して単一のテーブル行を読み取ります。 

*function.json* ファイルには `partitionKey` と `rowKey` が指定されています。 `rowKey` 値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

F# コードを次に示します。

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

### <a name="input---javascript-example"></a>入力 - JavaScript の例

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [JavaScript コード] (functions-reference-node.md) を示しています。 この関数は、キュー トリガーを使用して単一のテーブル行を読み取ります。 

*function.json* ファイルには `partitionKey` と `rowKey` が指定されています。 `rowKey` 値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>入力 - 属性
 
[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用してテーブル入力バインディングを構成します。

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  この属性のコンストラクターは、テーブル名、パーティション キー、および行キーを受け取ります。 次の例のように、out パラメーターまたは関数の戻り値で使用できます。

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  完全な例については、「[入力 - C# の例](#input---c-example)」を参照してください。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `Table` 属性の `Connection` プロパティ。
* `Table` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* 関数アプリの既定のストレージ アカウント ("AzureWebJobsStorage" アプリ設定)。

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `Table` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `table` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のテーブルまたはエンティティを表す変数の名前。 | 
|**tableName** | **TableName** | テーブルの名前。| 
|**partitionKey** | **PartitionKey** |省略可能。 読み取るテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 省略可能。 読み取るテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**take** |**Take** | 省略可能。 JavaScript で読み取るエンティティの最大数。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**filter** |**Filter** | 省略可能。 JavaScript のテーブル入力の OData フィルター式。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**connection** |**Connection** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>入力 - 使用方法

Table Storage の入力バインドは、次のシナリオをサポートしています。

* **C# または C# スクリプトで 1 行を読み取る**

  `partitionKey` と `rowKey` を設定します。 メソッド パラメーター `T <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 通常、`T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型です。 `filter` および `take` プロパティは、このシナリオでは使用しません。 

* **C# または C# スクリプトで 1 行または複数行を読み取る**

  メソッド パラメーター `IQueryable<T> <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型にする必要があります。 必要なフィルター処理があれば、`IQueryable` メソッドを使用して実行します。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。  

> [!NOTE]
> `IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で [CloudTable paramName メソッド パラメーターを使用](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)して、テーブルを読み取ります。

* **JavaScript で 1 行または複数行を読み取る**

  `filter` と `take` プロパティを設定します。 `partitionKey` と `rowKey` は設定しません。 `context.bindings.<name>` を使用して入力テーブルの単一のエンティティ (または複数のエンティティ) にアクセスします。 逆シリアル化されたオブジェクトには `RowKey` プロパティと `PartitionKey` プロパティがあります。

## <a name="output"></a>出力

Azure Table Storage の出力バインドを使用して、Azure Storage アカウントのテーブルにエンティティを書き込みます。

> [!NOTE]
> この出力バインドでは、既存のエンティティの更新はサポートされていません。 既存のエンティティを更新するには、[Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) の `TableOperation.Replace` 操作を使ってください。   

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、HTTP トリガーを使用して 1 つのテーブル行を書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルのテーブル出力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は複数のテーブル エンティティを書き込みます。

*function.json* ファイルを次に示します。

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

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

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルのテーブル出力バインドと、バインドを使用する [F# スクリプト](functions-reference-fsharp.md) コードを示しています。 この関数は複数のテーブル エンティティを書き込みます。

*function.json* ファイルを次に示します。

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

F# コードを次に示します。

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

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルのテーブル出力バインドと、バインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は複数のテーブル エンティティを書き込みます。

*function.json* ファイルを次に示します。

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs) を使用します。

この属性のコンストラクターは、テーブル名を受け取ります。 次の例のように、`out` パラメーターまたは関数の戻り値で使用できます。

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[入力 - 属性](#input---attributes)を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `Table` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `table` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。|
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | テーブルまたはエンティティを表す関数コードに使用される変数の名前。 `$return` に設定して、関数の戻り値を参照します。| 
|**tableName** |**TableName** | テーブルの名前。| 
|**partitionKey** |**PartitionKey** | 書き込むテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#output---usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 書き込むテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#output---usage)」セクションを参照してください。| 
|**connection** |**Connection** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

Table Storage の出力バインドは、次のシナリオをサポートしています。

* **任意の言語で 1 行を書き込む**

  C# または C# スクリプトでは、`out T paramName` などのメソッド パラメーター、または関数の戻り値を使用して、出力テーブル エンティティにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 *function.json* ファイルまたは `Table` 属性にパーティション キーと行キーが指定されている場合、`T` には任意のシリアル化可能な型を使用できます。 指定されていない場合、`T` には `PartitionKey` および `RowKey` プロパティを含む型を使用する必要があります。 このシナリオで、`T` は `ITableEntity` を実装するか `TableEntity` から派生するのが一般的ですが、必ずしもそうとは限りません。

* **C# または C# スクリプトで 1 行または複数行を書き込む**

  C# または C# スクリプトでは、メソッド パラメーター `ICollector<T> paramName` または `IAsyncCollector<T> paramName` を使用して、出力テーブル エンティティにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、追加するエンティティのスキーマを指定します。 `T` は `TableEntity` から派生するか、`ITableEntity` を実装するのが一般的ですが、必ずしもそうとは限りません。 *function.json* と `Table` 属性コンストラクターのパーティション キーと行キー値は、このシナリオでは使用しません。

  代わりに、Azure Storage SDK で `CloudTable paramName` メソッド パラメーターを使用してテーブルに書き込みます。

* **JavaScript で 1 行または複数行を書き込む**

  JavaScript 関数の場合、`context.bindings.<name>` を使用してテーブルの出力にアクセスします。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| テーブル | [テーブル エラー コード](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB、テーブル、キュー | [ストレージ エラー コード](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー | [トラブルシューティング](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
