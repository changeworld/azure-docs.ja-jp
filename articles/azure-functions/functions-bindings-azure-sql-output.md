---
title: Functions の Azure SQL 出力バインド
description: Azure Functions の Azure SQL 出力バインドを使用する方法について説明します。
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 37a9601f30c64108b079ec8573553a05088c8629
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493269"
---
# <a name="azure-sql-output-binding-for-azure-functions-preview"></a>Azure Functions の Azure SQL 出力バインド (プレビュー)

Azure SQL 出力バインドでは、データベースに書き込みできます。

セットアップと構成の詳細については、[概要](./functions-bindings-azure-sql.md)に関するページをご覧ください。


<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [Http トリガー、1 つのレコードを書き込む](#http-trigger-write-one-record-c)
* [Http トリガー、IAsyncCollector を利用してレコードを書き込む](#http-trigger-write-records-using-iasynccollector-c)

この例では `ToDoItem` 型とそれに対応するデータベース テーブルを参照します。

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-write-one-record-c"></a>

### <a name="http-trigger-write-one-record"></a>Http トリガー、1 つのレコードを書き込む

次の例は、キュー ストレージからのメッセージで提供されるデータを使用して、ドキュメントをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace AzureSQLSamples
{
    public static class WriteOneRecord
    {
        [FunctionName("WriteOneRecord")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
        {
            newItem = new ToDoItem
            {
                Id = req.Query["id"],
                Description =req.Query["desc"]
            };

            log.LogInformation($"C# HTTP trigger function inserted one row");
            return new CreatedResult($"/api/addtodo", newItem);
        }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### <a name="http-trigger-write-records-using-iasynccollector"></a>HTTP トリガー、IAsyncCollector を使用してレコードを書き込む

次の例では、[C# 関数](functions-dotnet-class-library.md) で、HTTP POST 本文の JSON に含まれるデータを利用し、レコード コレクションがデータベースに追加されます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.Products", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions の Azure SQL では現在のところ、JavaScript に対応していません。

# <a name="python"></a>[Python](#tab/python)

Azure Functions の Azure SQL では現在のところ、Python に対応していません。

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs) 属性を使用します。

この属性のコンストラクターは、SQL コマンド テキストと接続文字列設定名を受け取ります。 出力バインドの場合、SQL コマンド文字列は、データを格納するテーブルの名前になります。 接続文字列設定名は、Azure SQL または SQL Server インスタンスへの接続の[接続文字列](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString)が含まれるアプリケーション設定 (ローカル開発の場合は `local.settings.json` の) に合致します。

メソッド シグネチャでの `Sql` 属性の例を次に示します。

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions の Azure SQL では現在のところ、JavaScript に対応していません。

# <a name="python"></a>[Python](#tab/python)

Azure Functions の Azure SQL では現在のところ、Python に対応していません。

---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>次の手順

- [データベースからデータを読み取る (入力バインド)](./functions-bindings-azure-sql-input.md)
