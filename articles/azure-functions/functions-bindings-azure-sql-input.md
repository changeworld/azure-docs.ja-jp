---
title: Functions の Azure SQL 入力バインド
description: Azure Functions の Azure SQL 入力バインドを使用する方法について説明します。
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: 3c910ab85d3689eadfdf4ba32cc2702ce03ace65
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491091"
---
# <a name="azure-sql-input-binding-for-azure-functions-preview"></a>Azure Functions の Azure SQL 入力バインド (プレビュー)

Azure SQL 入力バインドにより、データベースからデータが取得され、関数の入力パラメーターに渡されます。

セットアップと構成の詳細については、[概要](./functions-bindings-azure-sql.md)に関するページをご覧ください。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-c)
* [HTTP トリガー、ルート データからの複数のドキュメントの取得](#http-trigger-get-multiple-items-from-route-data-c)

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

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のレコードを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 この関数は、クエリ文字列を使用して ID を指定する HTTP 要求によってトリガーされます。 その ID は、指定されたクエリを使用して `ToDoItem` レコードを取得するために使用されます。

> [!NOTE]
> HTTP クエリ文字列パラメーターは大文字と小文字が区別されます。
>

```cs
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItem
    {
        [FunctionName("GetToDoItem")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitem")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where Id = @Id",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Id={Query.id}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItem)
        {
            return new OkObjectResult(toDoItem.FirstOrDefault());
        }
    }
}
```

<a id="http-trigger-get-multiple-items-from-route-data-c"></a>

### <a name="http-trigger-get-multiple-items-from-route-data"></a>HTTP トリガー、ルート データからの複数の項目の取得

次の例は、クエリによって返されるドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 この関数は、ルート データを使用してクエリ パラメーターの値を指定する HTTP 要求によってトリガーされます。 このパラメーターは、指定されたクエリ内の `ToDoItem` レコードをフィルター処理するために使用されます。

```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace AzureSQLSamples
{
    public static class GetToDoItems
    {
        [FunctionName("GetToDoItems")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
        {
            return new OkObjectResult(toDoItems);
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

この属性のコンストラクターは、SQL コマンド テキスト、コマンドの種類、パラメーター、接続文字列設定名を受け取ります。 コマンドは、コマンドの種類 `System.Data.CommandType.Text` を指定した Transact-SQL (T-SQL) クエリ、またはコマンドの種類 `System.Data.CommandType.StoredProcedure` を指定したストアド プロシージャ名にすることができます。 接続文字列設定名は、Azure SQL または SQL Server インスタンスへの接続の[接続文字列](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString)が含まれるアプリケーション設定 (ローカル開発の場合は `local.settings.json` の) に合致します。

メソッド シグネチャでの `Sql` 属性の例を次に示します。

```csharp
    [FunctionName("GetToDoItems")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "gettodoitems/{priority}")]
            HttpRequest req,
            [Sql("select * from dbo.ToDo where [Priority] > @Priority",
                CommandType = System.Data.CommandType.Text,
                Parameters = "@Priority={priority}",
                ConnectionStringSetting = "SqlConnectionString")]
            IEnumerable<ToDoItem> toDoItems)
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

- [データベースにデータを保存する (出力バインド)](./functions-bindings-azure-sql-output.md)
