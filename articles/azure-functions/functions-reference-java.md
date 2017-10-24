---
title: "Azure Functions 用 Java 開発者向けリファレンス | Microsoft Docs"
description: "Java を使用して関数を開発する方法について説明します。"
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, java"
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/20/2017
ms.author: routlaw
ms.openlocfilehash: f8812c2e8ac3398dabd17feaf97897efca5566f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions の Java 開発者向けガイド
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>プログラミング モデル 

開発する Azure 関数は、入力を処理し出力を生成するステートレス クラス メソッドである必要があります。 インスタンスのメソッドを記述できますが、開発する関数は、クラスのインスタンス フィールドに依存することはできません。 すべての関数のメソッドには、`public` アクセス修飾子が必要です。

## <a name="triggers-and-annotations"></a>トリガーと注釈

通常、Azure 関数は、外部トリガーによって呼び出されます。 開発する関数は、トリガーとそれに関連付けられている入力を処理し、1 つ以上の出力を生成する必要があります。

入力と出力をメソッドにバインドするための Java の注釈は、`azure-functions-java-core` パッケージに含まれています。 サポートされる入力トリガーと出力バインドの注釈を次の表に示します。

バインド | 注釈
---|---
Cosmos DB | 該当なし
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | 該当なし
Notification Hubs | 該当なし
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobOutput`</li><li>`BlobOutput`</li></ul>
Storage Queue | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Storage Table | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Timer | <ul><li>`TimerTrigger`</li></ul>
Twilio | 該当なし

トリガーの入力と出力は、アプリケーション用の [function.json](/azure/azure-functions/functions-reference#function-code) に定義することもできます。

> [!IMPORTANT] 
> Azure Storage Blob、Queue、または Table のトリガーをローカルに実行するには、[local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) に Azure Storage アカウントを構成する必要があります。

注釈の使用例:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

注釈なしで記述された同じ関数:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

対応する `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>データ型

入力データと出力データでは、ネイティブ型、Java 型をカスタマイズした型、`azure-functions-java-core` パッケージに定義された特別な Azure 型を含む Java のすべての種類のデータ型を使用できます。 Azure Functions ランタイムが、受信した入力をコードで要求された型に変換します。

### <a name="strings"></a>文字列

関数のメソッドに渡された値は、関数の対応する入力パラメーターの型が `String` 型の場合は、文字列にキャストされます。 

### <a name="plain-old-java-objects-pojos"></a>Plain old Java object (POJO)

JSON でフォーマットされる文字列は、関数のメソッドの入力が特定の Java 型を予期している場合は、その Java 型にキャストされます。 この変換によって、自分のコードの中で変換を実装することなく、JSON 入力を関数に渡してコード内で Java 型を操作できます。

関数への入力として使用される POJO 型は、それが使用される関数のメソッドと同じ `public` アクセス修飾子を持っている必要があります。 POJO クラスのフィールドで `public` を宣言する必要はありません。 たとえば、JSON 文字列 `{ "x": 3 }` は、次の POJO 型に変換できます。

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>バイナリ データ

バイナリ データは、Azure 関数コードでは `byte[]` と表わされます。 バイナリの入力または出力を関数にバインドするには、function.json の `dataType` フィールドを `binary` に設定します。

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

その後、開発する関数コード内でそれを使用します。

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

バイナリ出力をバインドするには、`OutputBinding<byte[]>` 型を使用します。


## <a name="function-method-overloading"></a>関数のメソッドのオーバー ロード

関数のメソッドは、名前は同じだが型は異なるものでオーバーロードできます。 たとえば、1 つのクラスに `String echo(String s)` と `String echo(MyType s)` を設定でき、どちらを呼び出すかは、実際の入力の型を調べることで Azure Functions ランタイムが決定します (HTTP 入力では MIME の種類 `text/plain` は `String` になり、`application/json` は `MyType` を表します)。

## <a name="inputs"></a>入力

Azure Functions では、入力は、トリガー入力と追加入力という 2 つのカテゴリに分けられます。 `function.json` ではこれらは同じではありませんが、Java コードでは同じように使用されます。 次のコード スニペットを例として使用します。

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String in, @BindingName("item") MyObject obj) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` 注釈は、`function.json` に定義されたバインド/トリガーの名前を表す `String` プロパティを受け入れます。

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

したがって、この関数が呼び出されると、HTTP 要求のペイロードは、引数 `in` に `String` を渡し、引数 `obj` に Azure Table Storage の `MyObject` 型を渡します。

## <a name="outputs"></a>出力

出力は、戻り値または出力パラメーターの両方で表現できます。 出力が 1 つのみの場合は、戻り値を使用することをお勧めします。 複数の出力では、出力パラメーターを使用する必要があります。

戻り値は最も単純な出力形式であり、任意の型の値を返すと、Azure Functions ランタイムが実際の型に戻すことを試行します (HTTP 応答など)。 `functions.json` では、出力バインドの名前として `$return` を使用します。

複数の出力値を生成するには、`azure-functions-java-core` パッケージに定義されている `OutputParameter<T>` 型を使用します。 HTTP 応答と、キューへのメッセージのプッシュも行う必要がある場合は、次のようなコードを記述できます。

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputParameter;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, @BindingName("message") OutputParameter<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

さらに、`function.json` に出力バインドを定義します。

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "myqueue",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>特殊な型

場合によっては、関数で入力と出力を細かく制御する必要があります。 `azure-functions-java-core` パッケージには、要求情報を操作し、HTTP トリガーの戻り値の状態を調整するための特殊な型が用意されています。

| 特殊な型      |       ターゲット        | 一般的な用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage`  |    HTTP トリガー     | メソッド、ヘッダー、またはクエリを取得する |
| `HttpResponseMessage` | HTTP 出力のバインド | 200 以外の状態を返す   |

> [!NOTE] 
> `@BindingName` 注釈を使用して、HTTP ヘッダーとクエリを取得することもできます。 たとえば、`@Bind("name") String query` は、HTTP 要求ヘッダーとクエリを反復処理し、その値をメソッドに渡します。 たとえば、要求 URL が `http://example.org/api/echo?name=test` の場合、`query` は `"test"` になります。

## <a name="functions-execution-context"></a>Functions の実行コンテキスト

Azure Functions 実行環境との対話は、`azure-functions-java-core` パッケージに定義された `ExecutionContext` オブジェクト経由で行います。 開発するコード内で呼び出し情報と関数の実行時の情報を使用するには、`ExecutionContext` オブジェクトを使用します。

### <a name="logging"></a>ログの記録

`ExecutionContext` オブジェクトを介して Functions ランタイム ロガーにアクセスできます。 このロガーは、Azure モニターに関連付けられているため、関数の実行中に発生した警告とエラーのフラグを設定できます。

次のコード例は、受信した要求の本文が空の場合に警告メッセージをログに記録します。

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received in " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
