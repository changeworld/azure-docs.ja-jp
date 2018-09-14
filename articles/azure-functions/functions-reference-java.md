---
title: Azure Functions 用 Java 開発者向けリファレンス | Microsoft Docs
description: Java を使用して関数を開発する方法について説明します。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: f0dc471e8875ad0d738fce10421c3586752148b9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092311"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions の Java 開発者向けガイド

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>プログラミング モデル 

開発する Azure 関数は、入力を処理し出力を生成するステートレスなクラス メソッドである必要があります。 インスタンス メソッドを記述できますが、開発する関数は、クラスのインスタンス フィールドに依存することはできません。 すべての関数のメソッドには、`public` アクセス修飾子が必要です。

1 つのプロジェクトに複数の関数を含めることができます。 複数の jar に関数を分けることは避けてください。

## <a name="triggers-and-annotations"></a>トリガーと注釈

 Azure の関数は、HTTP 要求、タイマー、日付の更新などのトリガーで呼び出されます。 関数はそのトリガーと他の入力を処理して 1 つまたは複数の出力を生成する必要があります。

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) パッケージに含まれる Java の注釈を使用して、入力と出力をメソッドにバインドします。 注釈を使用するサンプル コードについては、各注釈に関する [Java リファレンス ドキュメント](/java/api/com.microsoft.azure.functions.annotation)と、Azure Functions のバインド リファレンス ドキュメント ([HTTP トリガー](/azure/azure-functions/functions-bindings-http-webhook)用など) を参照してください。

トリガーの入力と出力は、注釈を使用せずに、関数用の [function.json](/azure/azure-functions/functions-reference#function-code) で定義することもできます。 この方法で注釈の代わりに `function.json` を使用することはお勧めしません。

> [!IMPORTANT] 
> Azure Storage Blob、Queue、または Table のトリガーをローカルに実行するには、[local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) に Azure Storage アカウントを構成する必要があります。

注釈の使用例:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="third-party-libraries"></a>サードパーティ製ライブラリ 

Azure Functions はサード パーティ製ライブラリの使用をサポートしています。 既定で、プロジェクトの `pom.xml` ファイルで指定されているすべての依存関係は、`mvn package` 目標の間に自動的にバンドルされます。 `pom.xml` ファイルの依存関係として指定されていないライブラリの場合は、関数のルート ディレクトリ以下の `lib` ディレクトリに配置します。 `lib` ディレクトリに配置された依存関係は、実行時にシステム クラス ローダーに追加されます。

## <a name="data-type-support"></a>データ型のサポート

入力データと出力データでは、ネイティブ型、Java 型をカスタマイズした型、`azure-functions-java-library` パッケージに定義された特別な Azure 型を含む Java の任意のデータ型を使用できます。 Azure Functions ランタイムが、受信した入力をコードで要求された型に変換します。

### <a name="strings"></a>文字列

関数のメソッドに渡された値は、関数の対応する入力パラメーターの型が `String` 型の場合は、文字列にキャストされます。 

### <a name="plain-old-java-objects-pojos"></a>Plain old Java object (POJO)

JSON でフォーマットされる文字列は、関数の入力シグネチャが特定の Java 型を予期している場合は、その Java 型にキャストされます。 この変換によって、JSON で渡し、Java 型を使用できるようになります。

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

空の入力値は関数の引数として `null` になる可能性がありますが、空の可能性がある値を処理するには `Optional<T>` を使用することをお勧めします。


## <a name="function-method-overloading"></a>関数のメソッドのオーバー ロード

関数のメソッドは、名前は同じだが型は異なるものでオーバーロードできます。 たとえば、1 つクラスに `String echo(String s)` と `String echo(MyType s)` の両方を含めることができます。 Azure Functions では、入力の型 (HTTP 入力の場合、MIME の型 `text/plain` は `String` になりますが、`application/json` は `MyType` を表します) に基づいて呼び出すメソッドが決定されます。

## <a name="inputs"></a>入力

Azure Functions では、入力は、トリガー入力と追加入力という 2 つのカテゴリに分けられます。 `function.json` ではこれらは同じではありませんが、Java コードでは同じように使用されます。 次のコード スニペットを例として使用します。

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

この関数がトリガーされると、HTTP 要求は `String in` によって関数に渡されます。 エントリは、ルート URL の ID に基づいて Azure Table Storage から取得され、関数の本体で `obj` として使用されます。

## <a name="outputs"></a>出力

出力は、戻り値または出力パラメーターの両方で表現できます。 出力が 1 つのみの場合は、戻り値を使用することをお勧めします。 複数の出力では、出力パラメーターを使用する必要があります。

戻り値は最も単純な出力形式であり、任意の型の値を返すと、Azure Functions ランタイムが実際の型に戻すことを試行します (HTTP 応答など)。  戻り値の出力を定義するには、関数メソッドに出力の注釈を適用できます (注釈の name プロパティは $return にする必要があります)。

複数の出力値を生成するには、`azure-functions-java-library` パッケージに定義されている `OutputBinding<T>` 型を使用します。 HTTP 応答と、キューへのメッセージのプッシュも行う必要がある場合は、次のようなコードを記述できます。

たとえば、BLOB コンテンツのコピー関数は、次のコードのように定義することができます。 `@StorageAccount` の注釈は、`@BlobTrigger` と `@BlobOutput` 両方の接続プロパティの重復を防ぐためにここで使用されます。

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

`OutputBinding<byte[]`> を使用して (パラメーターの) バイナリ出力値を作成します。戻り値の場合は、単に `byte[]` を使用します。

## <a name="specialized-types"></a>特殊な型

場合によっては、関数で入力と出力を細かく制御する必要があります。 `azure-functions-java-core` パッケージには、要求情報を操作し、HTTP トリガーの戻り値の状態を調整するための特殊な型が用意されています。

| 特殊な型      |       ターゲット        | 一般的な用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP トリガー     | メソッド、ヘッダー、またはクエリを取得する |
| `HttpResponseMessage<T>` | HTTP 出力のバインド | 200 以外の状態を返す   |

> [!NOTE] 
> `@BindingName` 注釈を使用して、HTTP ヘッダーとクエリを取得することもできます。 たとえば、`@BindingName("name") String query` は、HTTP 要求ヘッダーとクエリを反復処理し、その値をメソッドに渡します。 たとえば、要求 URL が `http://example.org/api/echo?name=test` の場合、`query` は `"test"` になります。

### <a name="metadata"></a>Metadata

メタデータは、HTTP ヘッダー、HTTP クエリ、[トリガー メタデータ](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties) などのさまざまなソースから取得されます。 `@BindingName` 注釈をメタデータ名と一緒に使用して値を取得します。

たとえば、次のコード スニペットの `queryValue` は、要求された URL が `http://{example.host}/api/metadata?name=test` である場合 `"test"` になります。

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>実行コンテキスト

Azure Functions 実行環境との対話は、`azure-functions-java-library` パッケージに定義された `ExecutionContext` オブジェクト経由で行います。 開発するコード内で呼び出し情報と関数の実行時の情報を使用するには、`ExecutionContext` オブジェクトを使用します。

### <a name="custom-logging"></a>カスタムのログ記録

`ExecutionContext` オブジェクトを介して Functions ランタイム ロガーにアクセスできます。 このロガーは、Azure モニターに関連付けられているため、関数の実行中に発生した警告とエラーのフラグを設定できます。

次のコード例は、受信した要求の本文が空の場合に警告メッセージをログに記録します。

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>ログとトレースの表示

Java の標準出力とエラー ログ、その他の各種アプリケーション ログは、Azure CLI を使用してストリーム配信することができます。 まず、Azure CLI を使って、アプリケーション ログを出力するための構成を関数アプリケーションに対して行います。

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI を使って関数アプリのログ出力をストリーム配信するには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) コマンドには、`--provider` オプションを使って出力をフィルター処理するオプションが用意されています。 

Azure CLI を使ってログ ファイルを単一の ZIP ファイルとしてダウンロードするには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

このコマンドを実行する前に、Azure portal または Azure CLI でファイル システム ログを有効にしておく必要があります。

## <a name="environment-variables"></a>環境変数

セキュリティ上の理由から、キーやトークンなどの秘密情報はソース コードから保護してください。 関数コードのキーとトークンは、環境変数から読み込んで使用します。

Azure Functions をローカルに実行する場合に環境変数を設定するには、これらの変数を local.settings.json ファイルに追加することができます。 このファイルが関数プロジェクトのルートディレクトリに存在しない場合は、作成することができます。 このファイルは次のようになります。

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

`values` マップ内の各キー/値のマッピングは、`System.getenv("<keyname>")` を呼び出すことによってアクセスできる環境変数として実行時に使用可能になります (たとえば、`System.getenv("AzureWebJobsStorage")`)。 キー/値のペアの追加は認められており、そのような手法が推奨されています。

> [!NOTE]
> この方法を実行する場合は、local.settings.json ファイルをリポジトリの無視ファイルに追加して、それがコミットされないようにします。

これで、コードがこれらの環境変数に依存するようになったので、Azure portal にサインインして関数アプリ設定で同じキー/値のペアを設定することにより、コードがローカルでテストする場合と Azure にデプロイされた場合とで同等に機能するようになります。

## <a name="next-steps"></a>次の手順

Java による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使ったローカルでの開発とデバッグ 
* [Visual Studio Code を使用した Java Azure Functions のリモート デバッグ](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - `azure-functions:add` ゴールを使って関数の作成を効率化し、[ZIP ファイル デプロイ](deployment-zip-push.md)に向けてステージング ディレクトリを準備します。
