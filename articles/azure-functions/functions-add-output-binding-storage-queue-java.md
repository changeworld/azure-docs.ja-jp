---
title: Java 関数を Azure Storage に接続する
description: Queue storage の出力バインドを使用して、HTTP によってトリガーされる Java 関数を Azure Storage に接続する方法を学習します。
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272803"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Java 関数を Azure Storage に接続する

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

この記事では、[前のクイック スタートの記事](functions-create-first-java-maven.md)で作成した関数を Azure Storage キューと統合する方法を説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 この接続を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="prerequisites"></a>前提条件

この記事の作業を始める前に、[Java クイックスタートのパート 1](functions-create-first-java-maven.md) の手順を完了しておきます。

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

これで、Storage の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Java プロジェクトでは、バインドは関数メソッドのバインド注釈として定義されます。 その後、これらの注釈に基づいて *function.json* ファイルが自動的に生成されます。

_src/main/java_ の下の対象の関数コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`run` メソッド定義に、次のパラメーターを追加します。

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg` パラメーターは [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型です。これは、関数の完了時に出力バインドにメッセージとして書き込まれる文字列のコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`connection` メソッドによって設定されます。 接続文字列自体ではなく、ストレージ アカウントの接続文字列を含むアプリケーション設定を渡します。

`run` メソッドの定義は次の例のようになります。  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

これで、新しい `msg` パラメーターを使用して、関数コードから出力バインドに書き込むことができます。 成功応答の前に次のコード行を追加して、`name` の値を `msg` 出力バインドに追加します。

```java
msg.setValue(name);
```

出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

`run` メソッドは次の例のようになります。

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>テストを更新する

アーキタイプはテストのセットも作成するため、`run` メソッド シグネチャ内の新しい `msg` パラメーターを処理するためにこれらのテストを更新する必要があります。  

_src/main/java_ の下の対象のテスト コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`//Invoke` の下のコード行を次のコードに置き換えます。

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

これで、新しい出力バインドをローカルで試す準備ができました。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

前と同様に、次のコマンドを使用してプロジェクトをビルドし、Functions ランタイムをローカルで起動します。

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> host.json で拡張バンドルを有効にしていたため、スタートアップ時に[ストレージ バインド拡張機能](functions-bindings-storage-blob.md#add-to-your-functions-app)が他の Microsoft バインド拡張機能と共に自動的にダウンロードされ、インストールされました。

前と同様に、新しいターミナル ウィンドウで cURL を使用して、コマンド ラインから関数をトリガーします。

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

今回は、出力バインドはさらにストレージ アカウントに `outqueue` という名前のキューを作成し、この同じ文字列のメッセージを追加します。

次に、Azure CLI を使用して新しいキューを表示し、メッセージが追加されたことを確認します。 また、[Microsoft Azure Storage Explorer][Azure Storage Explorer] または [Azure portal](https://portal.azure.com) を使用してキューを表示することもできます。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>プロジェクトを再デプロイする 

公開したアプリを更新するには、次のコマンドを再度実行します。  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

ここでも、cURL を使用して、デプロイした関数をテストすることができます。 前と同様に、次の例のように、POST 要求の本文で値 `AzureFunctions` を URL に渡します。

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

再度 [Storage キュー メッセージを調べて](#query-the-storage-queue)、出力バインドが期待どおりにキューに新しいメッセージを生成することを確認できます。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 Python を使用して Azure Functions を開発する方法の詳細については、「[Azure Functions の Java 開発者向けガイド](functions-reference-java.md)」と、[Azure Functions のトリガーとバインド](functions-triggers-bindings.md)に関するページを参照してください。 Java での完全な関数プロジェクトの例については、[Java 関数のサンプル](/samples/browse/?products=azure-functions&languages=Java)を参照してください。 

次に、関数アプリに対して Application Insights の監視を有効にする必要があります。

> [!div class="nextstepaction"]
> [Application Insights との統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
